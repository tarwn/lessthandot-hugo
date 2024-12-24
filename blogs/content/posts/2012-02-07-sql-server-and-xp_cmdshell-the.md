---
title: SQL Server and xp_cmdshell – The good, the bad and more ugly
author: Ted Krueger (onpnt)
type: post
date: 2012-02-07T13:12:00+00:00
ID: 1515
excerpt: 'A little over a year ago I wrote an article that discussed calling SSIS packages from stored procedures.  One of the objectives of that article was to discuss why enabling xp_cmdshell can be dangerous in terms of potential security risks.  To further th&hellip;'
url: /index.php/datamgmt/dbprogramming/sql-server-and-xp_cmdshell-the/
views:
  - 19252
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
A little over a year ago I wrote an article that discussed calling [SSIS packages from stored procedures][1].  One of the objectives of that article was to discuss why enabling xp\_cmdshell can be dangerous in terms of potential security risks.  To further that discussion, this article will focus directly on xp\_cmdshell by discussing how extended procedures function and the risks that come with utilizing them with the newer versions of SQL Server.

Before we continue, let's discuss what xp_cmdshell as a general extended procedure is, and how it is processed when executed.    General extended procedures have been available with SQL Server since before version 2000.  These procedures are exactly what they are named, extensions into areas other than SQL Server, such as the operating system SQL Server is installed on.  There are many system extended procedures as well as the capability to write custom extended procedures.  Take a look at, "[Tutorial on Extended Stored Procedures for MS SQL Server v7.0][2]".  With SQL Server 7.0, administration tasks were more difficult than what DBAs and Developers have available in recent versions.  The thought of exiting SQL Server to obtain information, perform operating system level tasks, and even send emails, were tasks that the extended procedures almost exclusively were utilized for.  The extended procedure xp_cmdshell does this task by having a beginning execution in the context of SQL Server by passing a command that can be interpreted by the operating system, then returning some value to SQL Server, synchronously.

A perfect example of extended procedures and the functionality they once provided can be seen with sending email from SQL Server.  Prior to SQL Server 2005 and the introduction of Database Mail, sending emails from SQL Server was left to operating system level support.  [CDONTS][3] or CDO objects were utilized.  These libraries are objects that are still supported in Windows but can be considered crude and prone to hardships when attempting to do error handling with them or work with modern email systems.  CDONTS type libraries were called in SQL Server by creating an instance of an OLE object using [sp_OACreate][4].  This method is outside the scope of extended procedures, but the use of extended procedures was often used to prevent the use of sp\_OACreate by using xp\_cmdshell to call either VBScript, Executable, or other commands that would otherwise, perform the same OLE object instance creation.  Prior to features like Database Mail, these DLLs were critical to providing functions like this.  Sending a simple email from within a SQL Server task was forced to use extended procedures calling CDONTS or a secondary configuration utilizing Windows mail setup.  Such tasks like sending an email with SQL Server since 2005 may seem trivial, but the task truly was cumbersome in prior years.  File manipulation is also a task that was done through the use of extended procedures and other procedures that created external instances of objects or processes.  A basic XCOPY could be executed from SQL Server proving useful for tasks like backup file movements or retention periods.  As we can see from comparing CDONTS to Database Mail and file manipulation tasks, extended procedures are external libraries that can interact outside of SQL Server but be called from SQL Server.

Now that extended procedures have been defined, let's discuss the reasons they should be overlooked given the features of newer versions of SQL Server.  We will focus on the more commonly utilized, xp_cmdshell, so we can see the potential issues involved in the design and method of exposing extended procedures to SQL Server by enabling them.

> _Note: XP_CMDSHELL is disabled by default in SQL Server 2005 and newer versions._</p>
XP\_CMDSHELL does precisely what our definition of extended procedures has described: XP\_CMDSHELL executes an operating system level command and returns the output from that command to SQL Server.  With this, there are very few rules that apply also which starts to expose the potential issues in the procedure.  Now, _few rules,_ is in the eye of the person defining what a _few_ is.  Security requirements do play a part in having the ability to execute XP_CMDSHELL.  This is why it is adopted by DBAs more than anyone.  CONTROL SERVER is required which, in an uncontrolled administration team when all DBAs are sysadmins, leaves them all in this group.  An uncontrolled administration team allows for each individual to have the highest level of security clearance to both SQL Server and Windows.  A more stable and controlled level of security involves many levels of access.  These levels may include backup administrators, security administrators, Windows administrators, and so on.  A select few are then placed in a level of control and ability to perform all of these tasks.  Each task, including the controlling, comes with the responsibility and accountability of the abilities they have.

Even with the factor of the high-level security that is required to run XP_CMDSHELL, there are gaps in the scheme of the security itself.  For example, exposed system administrator (sa) accounts accompanied by the SQL Server service credentials that are chosen to run SQL Server Windows service, pose a great risk.

> _Note: Securing XP_CMDSHELL can be done in newer versions of SQL Server with the use of controlled proxy accounts and controlled administration teams.  As we will discuss later, there are better choices._</p>
When XP_CMDSHELL is executed, the command or Windows process that is later executed synchronously on the operating system uses the SQL Server service accounts credentials.  This allows the command that was called to do any task that the SQL Server service account can perform.  Unfortunately, a common practice in SQL Server installations also promotes the SQL Server service account into the local administrators groups on the operating system.  Something can make this unfolding chain of security exploitation even more frightening, the SQL Server service account having domain level privileges such as Domain Administrators group rights.

Stepping back from a high-level domain level security risk, take a normal installation of SQL Server Express on a laptop that has been issued to an employee.  SQL Server Express is often utilized for mobile data retention and later synchronizing with other data sources.  If the SQL Server service account of Express is set to local system, the Express instance becomes a risk itself.  Further this situation and expose the sa account as an account that is utilized and set the same across all Express installations.  For an application security plan, Windows authentication is common.  However, SQL Authentication is also commonly still enabled as a security mode.  This can be for updates to databases and Express instances while not requiring much thought in security required.  At this point, an instance of Express is installed (any version), Local System is the service account, a file share is exposed but secured on each laptop, and sa is enabled with a preset password of, pa$$word.  This entire setup allows for easy deployment and the updates just mentioned, and are extremely common.

At this stage in the installation discussed above, the SQL Server instance is somewhat secured.  An individual would have to know the sa account, and there has been no mention of XP\_CMDSHELL being enabled.   But, the installation of the application and SQL Server Express utilizes the silent install commands.  In that setup command, the sa account is in clear text.  This was found by a person looking to exploit the laptop in some way.  At this point, the person that means to cause harm to the laptop or network it has access to, can only exploit the databases on the Express instance as it is configured.  XP\_CMDSHELL is, however, can be easily enabled by performing the following reconfiguration of SQL Server.

```sql
EXEC master.dbo.sp_configure 'show advanced options', 1
RECONFIGURE
EXEC master.dbo.sp_configure 'xp_cmdshell', 1
RECONFIGURE
```


With the sa account having CONTROL SERVER, this command can be run.  Once XP_CMDSHELL is enabled, the operating system is exposed to commands that the SQL Server service account has rights to perform.

The folder share that was mentioned earlier and part of the installation has been secured to read only by users of the laptop.  The share can easily be seen by executing the following XP_CMDSHELL statement.

```sql
xp_cmdshell 'net view \.'
```


<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-110.png?mtime=1328627231"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-110.png?mtime=1328627231" width="292" height="191" /></a>
</div>

We have now exposed a local share named Imports.  Further attempts at trying to write files to the share have shown the share is indeed, secured.  That is, until the following xp_cmdshell statement is executed.

```sql
xp_cmdshell 'icacls \%COMPUTERNAME%Imports /t /grant Everyone:F'
```


<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-111.png?mtime=1328627231"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-111.png?mtime=1328627231" width="310" height="74" /></a>
</div>

Results

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-112.png?mtime=1328627231"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-112.png?mtime=1328627231" width="275" height="227" /></a>
</div>

As we can see, after the statement using XP_CMDSHELL was executed and the command utilizing CACLS was executed on the operating system under the local system account, the Imports share now contains the account "Everyone", with Full Control of the share.

 

Before concluding that we've successfully exposed this security risk, remember when domain level permissions were mentioned.  If the service account was running under an account that was the local administrator of the Windows machine, it could be shut down, apply malicious software, and perform an endless amount of harmful tasks on the local machine.  Add a domain level administrator and you could have exposure to employee personal information, risk to theft of funds, and access to any sensitive data stored on the domain.

**Conclusion**

In prior years, extended procedures were a proven tool in administration for completing tasks that were critical to maintaining stability and performance with SQL Server.  Recent years and =newer SQL Server versions have provided us with several other methods that are more stable and secure.  These features include Database Mail, SQL Server Integration Services, Replication, external utilization of PowerShell, Reporting Services, SQL Server Agent and many more.  With these and other features and options, exposing SQL server to executable tasks that inadvertently expose security risks simply isn't needed.

Becoming familiar with a process or task can be a problem in professional development of our skills.  This is shown in the use of XP_CMDSHELL over the years and prolonging its use when better methods exist.  Remaining adaptive to what has been developed as features and options in SQL Server is critical to removing the use of these outdated methods.  Take the time to develop with SQL Server and risks will become fewer in your environments.

 [1]: /index.php/DataMgmt/DBAdmin/execute-ssis-from-sql
 [2]: http://www.codeproject.com/Articles/414/Tutorial-on-Extended-Stored-Procedures-for-MS-SQL
 [3]: http://msdn.microsoft.com/en-us/library/ms526367%28v=exchg.10%29.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms189763.aspx