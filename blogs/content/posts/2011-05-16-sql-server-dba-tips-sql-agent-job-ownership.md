---
title: SQL Server DBA Tip 15 – SQL Server Agent – Job ownership
author: Ted Krueger (onpnt)
type: post
date: 2011-05-16T09:14:00+00:00
ID: 1176
excerpt: |
  Complexity of Job Ownership
  When a SQL Server Agent Job is created, the creator of the job is, by default, the owner of the job.  This can be important depending on the steps that the job is performing.  When a SQL Server Agent job runs, the agent uses&hellip;
url: /index.php/datamgmt/dbadmin/sql-server-dba-tips-sql-agent-job-ownership/
views:
  - 102332
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
**Complexity of Job Ownership**

When a SQL Server Agent Job is created, the creator of the job is, by default, the owner of the job.  This can be important depending on the steps that the job is performing.  When a SQL Server Agent job runs, the agent uses SETUSER to impersonate the owner of the job for the steps.  For example, if a job is executed that has two steps that are TSQL statements and the owner of the job has the needed security to run those TSQL statements, the job will run successfully.  However, if there is another step that calls a TSQL statement that uses a linked server with security on the linked server set to impersonate user context, the owner of the job will be used to access the linked server source. If the owner does not have security to access the linked server source, the job will fail due to security access errors. 

On the other side of how ownership is impersonated; permission issues with jobs can be fixed by simply changing ownership of the job.  For example: In SQL Server, there are two databases, SQLAgent\_1 and SQLAgent\_2.  Each database has a table named SQLAgent\_Tbl.  Database user Fred has been granted SELECT permissions to SQLAgent\_1.dbo.SQLAgent\_Tbl but not granted SELECT in SQLAgent\_2.dbo.SQLAgent\_Tbl.  A SQL Server Agent Job has been created with Fred as the owner.  In the job there are two steps.  Step one performs a select on SQLAgent\_1.dbo.SQLAgent\_Tbl and step two performs a select on SQLAgent\_2.dbo.SQLAgent_Tbl.  Upon executing the job the agent returns a severity 14 error with the following message.

<span class="MT_red">Executed as user: fred. The SELECT permission was denied on the object &#8216;Agent2_tbl&#8217;, database &#8216;SQLAgent_2&#8217;, schema &#8216;dbo&#8217;. [SQLSTATE 42000] (Error 229). The step failed.</span>

To fix this there are two options.  Grant Fred SELECT on SQLAgent2\_Tbl or change the ownership of the job to an account that already has the needed SELECT permissions.  In this example, change job ownership to the SQL Server Agent account which has SELECT permissions to both tables in both databases by using sp\_update_job procedure in the MSDB databases.

sql
Exec sp_update_job @job_name = 'AgentOwnerExample', @owner_login_name = 'DOMAINAgentAccount'
```


 

Upon executing the job again, it is successful.

**High-Level Security**

Security is handled differently when steps such as CmdExec are used, or other steps that start a new level of access and impersonation.   Steps such as CmdExec type are executed by the SQL Server Agent Account or a proxy account.  Unless ownership is explicitly changed, this typically is not a problem due to only members of the system administrator role (sysadmin) being capable of creating these steps.  However, there is no warning if membership is altered on a pre-existing step.  This allows for the situation to be encountered.

For example: The job that was explained earlier has a new step type of CmdExec added by a sysadmin.  The step calls a VBScript file that writes the system date to a logging file.  This can be done by adding a CmdExec step with a command of, “cscript.exe C:SystemDate.vbs” and then set the logging file output to C:textfile.txt.

This job will execute successfully under the system administrator account that created it and as long as the owner of the job is a sysadmin.  If the ownership is changed to fred, who only has SELECT for the first two steps, the job will fail with the following error.

Non-SysAdmins have been denied permission to run CmdExec job steps without a proxy account. The step failed.

From this, we know that the step requires the sysadmin role to begin the execution itself as well as the SQL Server Agent Service Account rights to the OS level commands being called.  For this situation, a proxy could be created to call the CmdExec.  The proxy would require access to the subsystem CmdExec then.  More problems are encountered even with a proxy calling this type of job though.  The logging to file mechanism also requires ownership of a sysadmin account.  We can see this by creating the proxy and then changing ownership to fred.  Even knowing the job saves successfully and allows fred to execute the CmdExec step, the job will fail with the following error.

<span class="MT_red">Warning: cannot write logfile C:testfile.txt. Writing to log files is only allowed to jobs that are owned by sysadmin. Please consider writing log to table. The step failed.</span>

The error message is useful in providing the workaround by logging to tables. 

**Conclusion**

As seen in these examples, ownership changes and settings provide a dynamic security aspect to the entire job and steps execution.  By using proxies, security can be set to follow a secure path in order to complete the required steps. 

**Resources**

[SQL Server Agent Roles][1]

[How CmdExec Works][2]

[SP\_UPDATE\_JOB][3]

[Changing Ownership of an Agent Job][4]

 [1]: http://msdn.microsoft.com/en-us/library/ms188283.aspx
 [2]: http://msdn.microsoft.com/en-us/library/ms190264.aspx
 [3]: http://msdn.microsoft.com/en-gb/library/ms188745.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms178031.aspx