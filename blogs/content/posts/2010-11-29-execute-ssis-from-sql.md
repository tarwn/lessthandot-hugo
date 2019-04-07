---
title: Run SSIS Package from Stored Procedure
author: Ted Krueger (onpnt)
type: post
date: 2010-11-29T11:41:31+00:00
ID: 960
excerpt: 'There are a few methods to execute a SQL Server Integration Services (SSIS) package from T-SQL.  Very often the use of xp_cmdshell is the first choice to accomplish this task.  Xp_cmdshell has primarily been a system administration extended stored procedure.  Many types of extended stored procedures such as this one are meant for tasks that are either manual or very refined and controlled tasks.  This is all due to the requirements of the levels of sysadmin roles - or CONTROL SERVER to be exact.  Further on this topic, xp_cmdshell is disabled by default because it has been a known attack method.  Having the ability to execute xp_cmdshell exposes operating system level access.  In worst case scenarios, the SQL Server Service account is also a domain account with either Domain Admin rights or rights to other resources on the domain that are sensitive or open to damaging effects to the business.  To expose xp_cmdshell then opens one of the highest security risks relating to SQL Server.'
url: /index.php/datamgmt/dbprogramming/execute-ssis-from-sql/
views:
  - 33852
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql server 2008
  - sql server integration services
  - ssis

---
There are a few methods to execute a SQL Server Integration Services (SSIS) package from T-SQL. Very often the use of xp\_cmdshell is the first choice to accomplish this task. Xp\_cmdshell has primarily been a system administration extended stored procedure. Many types of extended stored procedures such as this one are meant for tasks that are either manual or very refined and controlled tasks. This is all due to the requirements of the levels of sysadmin roles &#8211; or CONTROL SERVER to be exact. Further on this topic, xp\_cmdshell is disabled by default because it has been a known attack method. Having the ability to execute xp\_cmdshell exposes operating system level access. In worst case scenarios, the SQL Server Service account is also a domain account with either Domain Admin rights or rights to other resources on the domain that are sensitive or open to damaging effects to the business. To expose xp_cmdshell then opens one of the highest security risks relating to SQL Server. 

SSIS execution has many options. DTEXEC is a very common command line method to executing any DTSX file (SSIS Package) and has a vast amount of control with the ability of switches to set conditional values given the executing circumstances. DTEXEC can be manually executed from an OS level perspective. This leads to xp\_cmdshell and a number one method of execution solution. However, the security risks apply once xp\_cmdshell is enabled to accomplish this. Securing this method can be accomplished with steps to either secure the calling account to limited domain access. Another problem often arises in the needs of the SSIS processing itself. SSIS packages are often developed for the primary use of importing, transforming and inserting data from a wide range of sources that are not SQL Server. These sources may include files such as csv or txt, Excel and other Office Application files, Database servers like Oracle, DB2 or MySQL, Flat File or Desktop Database Applications: MS Access, FoxPro, Dbase.

Using the calling account with heightened security rights fixes the problem of access. Pushing the security away from the calling method and into the SSIS Package itself is often a secure and internal controlling method. The reasoning for this is the internal settings we can use to encrypt and otherwise promote other resources of securing sensitive data. Using the SQL Server Agent we can also utilize a built in structure to directly call SSIS packages. This requires the need to allow the Agent Service account the access we require. This is typically a much more controlled account that is only allowed access to areas that are designated to accomplish jobs defined by business needs or system processing needs. Going further, controlling the execution by use of proxy accounts can restrict access only to executing the SSIS packages and thus passing the proxy account to be authenticated to either directory services, executables outside SQL Server or database access.

To show this alternative and possibly more secure solution to executing an SSIS package from a stored procedure, the outlined process below can be used as a guideline. The process consists of a data flow task that will consume the results of a stored procedure and pump them into a table. The database being utilized will be AdventureWorks and the version of SQL Server is 2008 R2. 

The diagram below shows the flow of the starting point being the agent execution, step execution containing the SSIS package and then ending on the success and normal logging of the agent services.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_2.gif" alt="" title="" width="432" height="454" />
</div>

The SSIS package flow itself will follow the following chart

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_3.gif" alt="" title="" width="402" height="332" />
</div>

**Setting up a proxy for SSIS**

In preparation for setting the procedure to call the agent job, we need to create a proxy account. Setting up a proxy account to be used for calling a step a SQL Server Agent job starts with the creation of credentials in SQL Server.
  
A credential can be created with SSMS or T-SQL. With T-SQL, the CREATE CREDENTIAL statement is used.

sql
CREATE CREDENTIAL EmpImportUser WITH IDENTITY ='EmpImportUser'
,SECRET = 'EmployeeImportAccount'
GO
```

In SSMS, right click the Credentials tree under Security and select New Credential. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_4.gif" alt="" title="" width="309" height="193" />
</div>

The minimum requirement for the creation of a credential is the name and Windows identity that it will map to and store the password.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_5.gif" alt="" title="" width="628" height="254" />
</div>

The Credential is then utilized in the creation of the proxy account so the step in the job can be executed under the context of the credential with the stored password. 

Adding a proxy can be accomplished with SSMS and T-SQL as well. With T-SQL, the sp\_add\_proxy procedure is executed with mapping to the credential.

sql
EXEC msdb.dbo.sp_add_proxy @proxy_name=N'ImportUser',@credential_name=N'ImportUser', 
		@enabled=1
GO

EXEC msdb.dbo.sp_grant_proxy_to_subsystem @proxy_name=N'ImportUser', @subsystem_id=11
GO
```

With SSMS, right click the Proxies tree under the SQL Server Agent section

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_6.gif" alt="" title="" width="317" height="170" />
</div>

Ensure that the subsystem for this proxy is mapped to SSIS. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_7.gif" alt="" title="" width="628" height="449" />
</div>

Now complete the security setup by creating the login for machinenameImportUser with mappings to the AdventureWorks database. Later, we will grant the necessary permissions to the database and tables. The ImportUser login also needs to be mapped to MSDB and added to the db_ssisoperator role to allow the acount to execute SSIS packages.

**SSIS Package Creation**

Recall from the SSIS diagram in the beginning of this article that the package that will be used consists of an Execute SQL Task to ensure the table is dropped and recreated, the Data Flow Task consisting of a source and destination. Once the SQL Task is successful, the precedence moves to a single Data Flow task and so on. In order to make this flexible in changing the value and retaining the security we require a SQL Server Table configuration to be used in the SSIS package. A variable will be created named ID and will hold the value of the EmployeeID that we request. This will be used later when we show the creation of the procedure to call this process itself. This variable will be held in the configuration so we can access the value and update it by means of T-SQL UPDATE statements. This method allows the updating of the ID in the agent job by means of an UPDATE statement and securing down to the table level itself.

Another variable will act as the SqlCommand of the source in the data flow task. This will allow the dynamic setting of the parameter in the SqlCommand value of the source. Without the SqlCommand being formed at run-time, the statement would not be able to maintain a changing parameter for EmployeeID. This would then make the execution of the procedure limiting and unusable when parameters are required.

The Execute SQL Task will be a direct input of the following statement

sql
IF OBJECT_ID('dbo.EmpManagers') IS NULL
BEGIN
	CREATE TABLE [dbo].[EmpManagers](
	[RecursionLevel] [int] NULL,
	[EmployeeID] [int] NULL,
	[FirstName] [nvarchar](50) NULL,
	[LastName] [nvarchar](50) NULL,
	[ManagerID] [int] NULL,
	[ManagerFirstName] [nvarchar](50) NULL,
	[ManagerLastName] [nvarchar](50) NULL)
END
```

This statement checks if the table exists. If the table does not, it will be created. So this required CREATE TABLE rights to our user as well. If the create failed for any reason, a failed precedence would be used to handle the event along with event handlers. 

Once the Execute SQL Task is successful, the flow moves to the Data Flow Task. Initially, the ADO.NET Source takes a SqlCommand of
  
EXEC dbo.uspGetEmployeeManagers @EmployeeID=4

This will allow the mappings to be performed before we add the expression to build the execute procedure statement at run-time.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_8.gif" alt="" title="" width="387" height="278" />
</div>

The ADO.NET Destination now can point directly to the table, &#8220;dbo&#8221;.&#8221;EmpManagers&#8221;.
  
Once the Execute SQL Task and Data Flow Task are completed, move to create the variables for EmployeeID and SqlCmd.
  
EmployeeID will be named ID and a data type of Int32 matching the INT parameter data type in the procedure uspGetExployeeManagers. The SqlCmd variable will be a string data type and hold the following expression:
  
&#8220;EXEC dbo.uspGetEmployeeManagers @EmployeeID=&#8221; + (DT_WSTR, 10) @[User::ID]
  
If the variable has the value 4 preset, this expression evaluated will result in:
  
EXEC dbo.uspGetEmployeeManagers @EmployeeID=5

**Package Configuration**

Package configurations assist SSIS in becoming reusable and dynamic. They also are used to secure sensitive data such as passwords, data sources and user accounts. For our use, the ID variable will be held in a SQL Server Table Configuration. Many configuration types exist. XML File for example can be used to hold configurations in an XML formatted file. This however will mean we need added security to that file itself. In this process we are attempting to secure this calling method as far as we can. Taking the file aspect out of the equation helps us do this. 

To see how to create a configuration file follow the steps outlined here.

Below shows a select of the table after completing the creation of the SQL Server Table Configuration.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_9.gif" alt="" title="" width="628" height="151" />
</div>

**The SQL Agent Job**

SQL Agent Jobs can be created with T-SQL and SSMS. SSMS is a good control to use for agent jobs as you do not gain much in configurations with using the T-SQL procedures themselves. The use of the procedures is good when SSMS is not an option or has failed.

To create the job to call the SSIS package, right click the Jobs node in the SQL Agent tree and select, New Job.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_10.gif" alt="" title="" width="372" height="118" />
</div>

The name of our job will be CallSSIS and will consist of one step. The step will be a SQL Server Integration Services type and connect to the instance that the package created earlier was deployed to. Recall the proxy account that was created earlier was named, ImportUser. This proxy account should be selected as the Run as account.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_11.gif" alt="" title="" width="400" height="195" />
</div>

Once this is completed, save the job. Executing the job at this point is a good idea to ensure no problems exist. This allows for troubleshooting internal problems either to the security of the proxy account or the SSIS package itself. Adding the procedure to call the job at this point without testing the job would add a level to the process that would increase the difficulty of troubleshooting any problems.

**The controlling procedure**

Now that all of the pieces have been created the procedure that will perform the actual execution of the SSIS package by means of starting the SQL Agent job itself can be created.

To start a job from T-SQL the system procedure sp\_start\_job is used. Sp\_start\_job takes several parameters as we can see below

> Execute sp\_start\_job
       
> { [@job\_name =] &#8216;job\_name&#8217;
         
> | [@job\_id =] job\_id }
       
> [ , [@error\_flag =] error\_flag]
       
> [ , [@server\_name =] &#8216;server\_name&#8217;]
       
> [ , [@step\_name =] &#8216;step\_name&#8217;]
       
> [ , [@output\_flag =] output\_flag]

To successfully call sp\_start\_job, we are only required the job name or job id. The remaining parameters can be left NULL. So to call the CallSSIS job the execute statement would be:

sql
Exec msdb.dbo.sp_start_job @job_name = N'CallSSIS'
```

Note that the parameters are in Unicode and should be converted as such.

With any statement, error handling should be used. In this example, adding the TRY…CATCH and utilizing RAISERROR can be used to handle exceptions in the execution of sp\_start\_job.

The complete stored procedure, CallUpEmpCheck, would be as follows

sql
CREATE PROCEDURE [dbo].[CallUpEmpCheck] 
(@ID INT = NULL)
AS
    IF @ID IS NOT NULL  
	 BEGIN
		UPDATE dbo.[SSIS Configurations]
		SET ConfiguredValue = @ID
		WHERE PackagePath = 'Package.Variables[User::ID].Properties[Value]';
		Exec msdb.dbo.sp_start_job @job_name = N'CallSSIS';
	 END
	ELSE
	 BEGIN
	    RAISERROR('Send right to catch!',16,1);
	 END
```

Once the procedure is created we are ready to execute and test the process completely through. Note that the UPDATE statement has been added to the procedure to ensure the EmplyeeID that we pass is updated in the configuration table.

**Security**

Earlier the ImportUser login was created and added to the operator role for SSIS in order to execute SSIS packages. In order to work with the tables in this process further right must be granted.

sql
GRANT UPDATE ON dbo.[SSIS Configurations] TO [ONPNT_XPSImportUser]
GRANT INSERT ON dbo.EmpManagers TO [ONPNT_XPSImportUser]
GRANT SELECT ON dbo.EmpManagers TO [ONPNT_XPSImportUser]
GRANT CREATE TABLE TO [ONPNT_XPSImportUser]
GRANT EXECUTE ON dbo.uspGetEmployeeManagers TO [ONPNT_XPSImportUser]
```

This may seem like a lot of security for this user and in the context of AdventureWorks, CREATE TABLE is a higher level setting. When we put this into perspective to the SQL Server and even the context of AdventureWorks, the security is restricted to the configuration table, which is only damaging on execution of the SSIS package. Then the SELECT rights due to the SSIS processing in the data flow task and the INSERT. These are limited to the EmpManagers table which is related (in our demo) to the one user run processing. The user at that point is owner of that table and process. So we secured to a point much more controlled than the alternatives of SQL Server level rights by moving down to table rights and procedure execution rights.

**Execution of the work**

Executing this setup is the final test. Use the following execute procedure statement to start the process

sql
EXEC dbo.[CallUpEmpCheck] @ID = 5
```
</p> 

The resulting message in the query window will be:

> (1 row(s) affected)
  
> Job &#8216;CallSSIS&#8217; started successfully.

The first message is from the update to the configuration table and the second message is returned from the sp\_start\_job execution.

Validating the data has been inserted into the EmpManagers table:

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/callssisfromproc_12.gif" alt="" title="" width="622" height="260" />
</div>

**The downfall**

There is an obvious problem with this method. Error handling the call itself to SSIS is difficult. The options to handle this are to verify the logging from the SSIS execution, the SQL Agent job logs them or push event handlers in the SSIS package itself to have a much more dominant role in the handling of a failed execution. As long as we capture the starting point of execution (the procedure), the middle tier of the process (the AW procedure) and the final stage of the insertion of the data, we accomplish the task.