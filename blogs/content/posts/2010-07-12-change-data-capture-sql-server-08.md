---
title: Change Data Capture rundown 100 (setup)
author: Ted Krueger (onpnt)
type: post
date: 2010-07-12T21:46:57+00:00
ID: 842
excerpt: |
  Many times as a database administrator, you're going to find yourself being asked, "When did this change?"  In fact, it is a common question that can come in so often, if you don't have the answer readily available, the business will quickly become frustrated with you.  This can have an adverse effect on your reputation of being able to manage the databases in question.
url: /index.php/datamgmt/datadesign/change-data-capture-sql-server-08/
views:
  - 13144
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - cdc
  - change data capture
  - new features
  - sql server 2008
  - tracking data

---
Many times as a database administrator, you're going to find yourself being asked, "When did this change?" In fact, it is a common question that can come in so often, if you don't have the answer readily available, the business will quickly become frustrated with you. This can have an adverse effect on your reputation of being able to manage the databases in question. 

Some databases (and software packages) simply do not track changes in the database. The job of the system is to do what the business wants and that typically does not initially include data tracking. Logging takes time and precious performance away from the systems. That performance hit can make a software vendor look bad. Is it worth the performance cost and cost to develop the tracking solution to them? I think we know the answer to that question in most cases. I'm on their side in the case of where you put your money for the product you provide to a point. 

Putting all of the bickering with why or why not vendors add data tracking to overall system features aside, let's look at how we can manage data tracking with SQL Server and leave the vendor to do what the business asks of them; provide the solution to keep the business running.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/cdc.gif" alt="" title="" width="384" height="304" />
</div>

In SQL Server 2008, Change Data Capture was introduced. Oh, that sounds interesting and something you can use! Well, there is a cost just like most features have cost that go with them. Remember the performance issue with the vendor doing data tracking? It comes with CDC as well. As with everything, performance can take a back seat to providing the business with what they need, so we're going to show you how to set CDC up as a base data tracking process in your databases.

**Setup CDC**

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/cdc_2.gif" alt="" title="" width="162" height="154" align="left" />
</div>

We are going to go through a step-by-step to get CDC going without falling into the deep end and then discuss the setup and overall process after accomplishing the setup.



To ensure CDC is not setup, we can check sys.databases and the is\_cdc\_enabled column. You will see either a 1 for enabled or 0 for disabled. 

```sql
SELECT database_id ,
        name ,
        is_cdc_enabled
FROM sys.databases
```

Security for CDC is handled differently at each level from the database to the tables you enlist. You must first enable CDC for the database itself. In order to accomplish this you must be in the sysadmin server role. Once enabled on the database level, any database user that is in the db_owner database role can then enable tables in the database for CDC.

If is\_cdc\_enabled is set to 0, use sys.sp\_cdc\_enable\_db to enable CDC. sys.sp\_cdc\_enable\_db is run in the context of the database you wish to enable. So, if you were working in AdventureWorks and wanted CDC enabled, you would want to use a USE AdventureWorks statement to move into the context first.

Below is something you could run as a check and enable task:

```sql
If (select is_cdc_enabled from sys.databases where [name] = 'AdventureWorks') = 0
 begin
	Exec sys.sp_cdc_enable_db
	print 'CDC Enabled'
 end
```

If you run into a failure to authenticate:
  


> Msg 22830, Level 16, State 1, Procedure sp\_cdc\_enable\_db\_internal, Line 186
  
> Could not update the metadata that indicates database AdventureWorks is enabled for Change Data Capture. The failure occurred when executing the command 'SetCDCTracked(Value = 1)'. The error returned was 15404: 'Could not obtain information about Windows NT group/user 'PMHCtkrueger', error code 0x54b.'. Use the action and error to determine the cause of the failure and resubmit the request.
  
> Msg 266, Level 16, State 2, Procedure sp\_cdc\_enable\_db\_internal, Line 0
  
> Transaction count after EXECUTE indicates a mismatching number of BEGIN and COMMIT statements. Previous count = 0, current count = 1.
  
> Msg 266, Level 16, State 2, Procedure sp\_cdc\_enable_db, Line 0
  
> Transaction count after EXECUTE indicates a mismatching number of BEGIN and COMMIT statements. Previous count = 0, current count = 1.

It is due to a bad owner state. You can fix this by running: 

```sql
exec sp_changedbowner 'sa','dbo'
```

Now that the database is enabled for CDC, we can move to enabling a table to start tracking change in the data. 

**Enabling a table**

CDC can track an entire table or specific columns in a table. You can use the @captured\_column\_list parameter when executing sys.sp\_cdc\_enable_table to specify the columns. If the parameter is left out or NULL, the entire table will be then tracked. For performance reasons, design CDC for the table with great thought in capturing only what is needed. 

**CDC and Filegroups** 

BOL best practice suggests putting the CDC tables (instances) into a designated filegroup. I can see this benefiting performance and not affecting the tables under the CDC monitoring. This does add a structure change to the database so plan this out as well. Move the filegroup to a designated disk when possible. IO is always an important consideration. If IO becomes a problem later on, it is one of the harder to fix performance issues. This is because it requires moving files while working on the disk, which entails downtime on the database. 

We will show the filegroup practice so let's create a filegroup for our CDC instance.

**Create a filegroup** 

```sql
USE master
GO
ALTER DATABASE AdventureWorks
ADD FILEGROUP CDCDataStore;
GO
ALTER DATABASE AdventureWorks
ADD FILE 
(
    NAME = CDCDataStore,
    FILENAME = 'C:Program FilesMicrosoft SQL ServerMSSQL10.MSSQLSERVERMSSQLDATACDCDataStore.ndf',
    SIZE = 500MB,
    MAXSIZE = 1000MB,
    FILEGROWTH = 500MB
)
TO FILEGROUP CDCDataStore;
GO
```

To enable CDC, we use sys.sp\_cdc\_enable_table as mentioned earlier. 

Run the following to enable CDC on the table Employee in the AdventureWorks database (the entire table will be monitored):

```sql
USE AdventureWorks
GO
EXEC sys.sp_cdc_enable_table
@source_schema = N'HumanResources',
@source_name   = N'Employee',
@role_name     = N'CDCRoles',
@filegroup_name = N'CDCDataStore',
@supports_net_changes = 1
GO
```

> <span class="MT_red">Note: cdc agents need to be running, so the SQL Server agent needs to be running.</span>

If SQL Agent is not running, you will receive errors when the cdc table enable procedure executes. Since there are four of these agents, you will receive the error for each job creation attempt

> SQLServerAgent is not currently running so it cannot be notified of this action.
  
> SQLServerAgent is not currently running so it cannot be notified of this action.
  
> SQLServerAgent is not currently running so it cannot be notified of this action.
  
> SQLServerAgent is not currently running so it cannot be notified of this action.

Once you execute the enabling table procedure, some table valued functions will be created. All and net. These will be used to look into the instances of CDC. What we need to know to look into the changes captured are the LSN we want to start with and end with. 

To obtain the latest and first LSN we can use sys.fn\_cdc\_getmax_lsn and min:

```sql
select sys.fn_cdc_get_min_lsn('HumanResources_Employee');
select sys.fn_cdc_get_max_lsn
```


**Putting this to work to review CDC**

```sql
declare @begin binary(10), @end binary(10);
set @begin = sys.fn_cdc_get_min_lsn('HumanResources_Employee');
set @end = sys.fn_cdc_get_max_lsn();
select * from cdc.fn_cdc_get_all_changes_HumanResources_Employee(@begin,@end,'all update old');
go
```

Time to play with what we've setup on the Employee table. Let's break login id for employeeid 1

```sql
select * from HumanResources.Employee
--adventure-worksguy1 „²we'll take the first one
update HumanResources.Employee
set LoginID = 'adventure-worksguy' 
where EmployeeID = 1
```

CDC should have captured the change of the string adventure-worksguy1 to adventure-worksguy

The nice thing here is CDC will look at the data and if nothing changes, even for a DML event, nothing will be written. Only when a change is caught on the columns monitored will it be logged. Again, we can see how this can be a performance factor so plan this well when implementing and use CDC in the appropriate places.

When we check CDC we can see we get representation of the data before and after logged

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/cdc_3.gif" alt="" title="" width="722" height="323" />
</div>

Now the performance hit is imaginable already. 

  1. Logging the before 
  2. Logging the after

But we should really test that. We'll do that with simply logging the times from client statistics to show a rough estimation of performance between a CDC enabled data change and a non-CDC table enabled data change.

We already have CDC enabled, so executing the Update statement again, the cost is 29ms on average of 15 executions. 

To disable CDC on a table we use the, sys.sp\_cdc\_disable_table procedure as shown below:

```sql
exec sys.sp_cdc_disable_table 
  @source_schema = 'HumanResources', 
  @source_name = 'Employee',
  @capture_instance = 'HumanResources_Employee'
```

After disabling CDC table instance for Employee, we hit 11ms averages.

Putting that into perspective: almost triple the cost!

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/cdc_4.gif" alt="" title="" width="486" height="348" />
</div>

So CDC has an enormous cost with it, but in a database where logging is absent and development is out of our hands along with the question of, "Why and who changed this?" being asked often, CDC will be an option that should be brought into play to manage the change control of the database.

Some things that should be considered are ETL loads. I would absolutely disable CDC on tables where you load large amounts of data and then enable it after the load is completed. The time for loading could be double to triple after enabling tables for CDC. That may not be acceptable in the activity of the table. 

Now that we have gone over the setup of CDC, we will move into using SSIS and CDC together and methods where CDC really shows its value. Look for that article soon.