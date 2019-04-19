---
title: Use the sys.database_mirroring DMV to quickly check if the databases are in principal or mirror role and what state they are in
author: SQLDenis
type: post
date: 2013-01-07T15:39:00+00:00
ID: 1904
excerpt: 'I have been using mirroring for about three years now and I must say it is one of the best features that have been added to SQL Server. We used to do plain old replication and log shipping in the past but almost all of those have been replaced by mirror&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/use-the-sys-database_mirroring-dmv/
views:
  - 31732
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - ha/dr
  - mirroring
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
I have been using mirroring for about three years now and I must say it is one of the best features that have been added to SQL Server. We used to do plain old replication and log shipping in the past but almost all of those have been replaced by mirroring. 

Someone at work asked me how to check what state the databases are in and if a database on one server is the principal or the mirror. There are a couple of ways you can check this. You can open up SSMS and expand the database folder. You will see something like the following next to the database name _(Mirror, Synchronized / Restoring...)_ or you might see _(Principal, Synchronized)_, other states are possible, for example disconnected, synchronizing, suspended.

Another way to see what is going on is to launch Database Mirroring Monitor. You launch the Database Mirroring Monitor by right clicking on the database, selecting Tasks, then click on Launch Database Mirroring Monitor...

Another option is to check the error log, messages might look like these

_Message
  
Database mirroring is inactive for database 'YourDB'. This is an informational message only. No user action is required.</p> 

Message
  
The mirroring connection to “TCP://SomeServer.SomeNetwork:5022” has timed out for database “YourDB” after 10 seconds without a response. Check the service and network connections.</em>

I myself like the sys.database_mirroring dynamic management view. This view will give you the state that the mirror is in, the partner name, mirror role, what the safety level is, the connection timeout and more. Here is a query I like to run

```sql
SELECT	db_name(sd.[database_id])              AS [Database Name]
		  ,sd.mirroring_state                  AS [Mirror State]
		  ,sd.mirroring_state_desc             AS [Mirror State] 
		  ,sd.mirroring_partner_name           AS [Partner Name]
		  ,sd.mirroring_role_desc              AS [Mirror Role]  
		  ,sd.mirroring_safety_level_desc      AS [Safety Level]
		  ,sd.mirroring_witness_name		   AS [Witness]
		  ,sd.mirroring_connection_timeout AS [Timeout(sec)]
    FROM sys.database_mirroring AS sd
    WHERE mirroring_guid IS NOT null
    ORDER BY [Database Name];
```

Here are the different mirroring states and what it means, you can found this information here in Books On Line : http://msdn.microsoft.com/en-us/library/ms189284(v=sql.110).aspx

> **SYNCHRONIZING**
  
> The contents of the mirror database are lagging behind the contents of the principal database. The principal server is sending log records to the mirror server, which is applying the changes to the mirror database to roll it forward.
  
> At the start of a database mirroring session, the database is in the SYNCHRONIZING state. The principal server is serving the database, and the mirror is trying to catch up.
> 
> **SYNCHRONIZED**
  
> When the mirror server becomes sufficiently caught up to the principal server, the mirroring state changes to SYNCHRONIZED. The database remains in this state as long as the principal server continues to send changes to the mirror server and the mirror server continues to apply changes to the mirror database.
  
> If transaction safety is set to FULLautomatic failover and manual failover are both supported in the SYNCHRONIZED state, there is no data loss after a failover.
  
> If transaction safety is off, some data loss is always possible, even in the SYNCHRONIZED state.
> 
> **SUSPENDED**
  
> The mirror copy of the database is not available. The principal database is running without sending any logs to the mirror server, a condition known as running exposed. This is the state after a failover.
  
> A session can also become SUSPENDED as a result of redo errors or if the administrator pauses the session.
  
> SUSPENDED is a persistent state that survives partner shutdowns and startups.
> 
> **PENDING_FAILOVER**
  
> This state is found only on the principal server after a failover has begun, but the server has not transitioned into the mirror role.
  
> When the failover is initiated, the principal database goes into the PENDING_FAILOVER state, quickly terminates any user connections, and takes over the mirror role soon thereafter.
> 
> **DISCONNECTED**
  
> The partner has lost communication with the other partner. 

Start using this dynamic management view to check the state of your mirrored databases. Also be aware that you can set thresholds, one way to set thresholds is by using the Database Mirroring Monitor. Thresholds can be found after clicking on the Warnings tab 

[<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/MirroringThresholds.PNG?mtime=1357579980" width="398" height="169" />][1]

What is your preferred way of checking what mirroring state the databases are in?

 [1]: /wp-content/uploads/blogs/DataMgmt/Denis/MirroringThresholds.PNG?mtime=1357579980