---
title: SQL Server DBA Tip 10 – SQL Server Reporting – Offload the reporting factor
author: Ted Krueger (onpnt)
type: post
date: 2011-05-09T10:21:00+00:00
excerpt: 'A primary purpose of retaining data is to view that data at some point.  Even when database servers have a largely OLTP basis, the need to retrieve data from the database will arise.  Retrieving data in some manner is referred to as reporting.  This rea&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-10-offload-the-reporting-factor/
views:
  - 9394
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
A primary purpose of retaining data is to view that data at some point.  Even when database servers have a largely OLTP basis, the need to retrieve data from the database will arise.  Retrieving data in some manner is referred to as reporting.  This reason the term reporting is defined here is to focus on the concept of reporting as something not only done with a feature like SQL Server Reporting Services.  SQL Agent Jobs, notifications, proactive troubleshooting steps, Data Warehouse loading and other ETL processing are all a form of retrieving data.  These can all be identified as a form of reporting on the database. 

When reporting is mixed into a configuration of SQL Server that was set to handle more writes than reads (pushing to OLTP concepts), the configuration may not handle the type of requests to satisfy reporting very well.  The results from running reports on configurations that are not geared for large result sets can be found in problems such as parallelism, memory pressure or long blocking to possible deadlocking events.    There are ways to _offload_ these tasks in SQL Server to prevent the pressure that they may bring with them

**Design Considerations**

When reporting becomes a concern due to pressure it is putting on the primary focus of a SQL Server database, there are questions that need to be answered before making the decision of how to offload the pressure.

  1. How up-to-date does the data need to be in order to retain business continuity?
  2. What physical resources do I have available already?
  3. What are my cost constraints?
  4. Do I need “all” the data to successfully satisfy reporting requirements?
  5. Is this even possible?
  6. Is a standard operating procedure in the business a better option?

These six questions are the start to questions that will branch off once they are answered.  For a DBA, question one will dictate the design the most.  How current the data needs to be will make the difference in what options are available.  If the requirement allows for data to be greater than or equal to a 24 hour period, options are much greater.  If the requirement forces data to be current to within ten minutes, there are fewer options built into SQL Server.

**SQL Server available options** 

SQL Server has many built-in options that can be utilized for offloading reporting.  Utilizing these options is ideal to minimize cost.  Using built in options also allows for less training if there are already in-house SQL Server skills and the owner of the process is familiar with SQL Server features.   The features that will be focused on today are Backup and Restore, Database Snapshots, Log shipping with Standby, SSIS and Replication.

We can put features available into categories based on the up-to-date factor.

  * **Greater than or equal to 24 hours** 
      * Backup and Restore
      * Database Snapshot (with or without Mirroring)
      * Log Shipping with Standby
      * ETL – SQL Server Integration Services
  * **Greater than or equal to 12 hours** 
      * Backup and Restore
      * Database Snapshot (with or without Mirroring)
      * Log Shipping with Standby
      * ETL – SQL Server Integration Services

  * **Greater than or equal to 1 hour** 
      * Database Snapshot (with or without Mirroring)
      * Log Shipping with Standby
      * ETL – SQL Server Integration Services
      * SQL Server Replication
  * **Nearly Real-time** 
      * SQL Server Replication
      * ETL – SQL Server Integration Services

 

Notice SSIS is listed in every area.  SSIS has the ability to perform a massive change to a secondary data area or an incremental change.  This allows the flexibility of it being utilized in many scenarios. 

This is only a guideline that has worked well over the years.  There are many variables that make this listing extremely dynamic.  Since those variables are dynamic based on the business and the exact environment they are being considered for, we will focus on the reasons each feature is placed into the respected area.

**Backup and Restore**

Backup and Restore availability for use in offloading reporting will depend heavily on the size of the database that is being offloaded.  A database that is 100GB in size with a backup file size of 70GB, is much more manageable than a database that is 2TB in size with a backup file size of 1.5TB.

Space considerations are also a factor in utilizing this method.  Production databases are typically set in Full Recovery Model to ensure a point in time recovery.  This includes a properly sized transaction log file.  Transaction Log files can range from 2GB up to 100GB depending on the needs.  When a database is backed up, the utilized space is reflected in the actual backup file size.  However, when a restore operation is performed from that backup file, the space required is the full size based off both data and log physical sizes when the backup was taken.   

If disk space is available, Backup and Restore is a great option for data that can be 24 hours old.  The database is copied to the new location for reporting and the added benefit of testing restore tasks is added.  Ensure that the operation of copying the backup files to the reporting location is performed during times that will not affect the business use of the network.  Backup and Restore also has the ability to restore partial databases.  This can be utilized to restore only certain tables that are required for reporting.  Although this option was worth mentioning; a database in most cases should not be configured to use filegroups and files based only on the need for reporting.  However, this configuration can be beneficial if the amount of spindles is allocated to move these filegroups onto them.  This promotes performance of multiple disk heads and the ability to restore partial databases.

**Database Snapshot (with or without Mirroring)**

 

Note: Database Snapshot feature is an Enterprise Edition feature only

Database snapshots are another option that fits when data can be older than an hour.  The use of snapshots may be increased to greater than 12 hours due to the size of a database.   There are some limitations with Database Snapshots and can all be [found in BOL][1].  The primary concern is I/O usage while the snapshot is in process.  This <span style="text-decoration: line-through;">can</span> resource usage can be lowered by adding Mirroring into the landscape.  Database Mirroring allows for Database Snapshots to be taken on the mirror.  This allows for the principal in the mirroring session to be left out of the full snapshot operations.  Although the mirror takes on added pressure and will affect the commit rates of the mirroring session, Asynchronous Mirroring can be configured to allow for the mirroring session to not directly affect the performance of the principal database. 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-50.png?mtime=1303933999"><img src="/wp-content/uploads/blogs/DataMgmt/-50.png?mtime=1303933999" alt="" width="603" height="179" /></a>
</div>

Database Snapshots are only as current as the time they were taken.  In an asynchronous mirroring with snapshots landscape in place, in most case, can fit into the less than an hour level of offloading reporting. One important factor with Asynchronous Mirroring and Snapshots; you cannot execute a snapshot while a mirroring session is actively synchronizing. This can be difficult to catch so taking a mirroring session into a paused state is sometimes performed.

 

**Log Shipping with Standby**

 

Log shipping performed to a standby database (read-only) is not common and has one sever limitation, but should be considered.  The sever limitation with this utilization of log shipping is, users are disconnected when the schedule log backup is restored to the standby database.  This can be ideal in some business situations but in most, the disconnection of users can be a frustrating event.  If log growth is minimal and the time span in the log shipping schedules is greater than an hour, the option can be considered.

**ETL – SQL Server Integration Services**

 

SQL Server Integration Service (SSIS) is SQL Server’s Export, Transform and Load platform.  SSIS is listed in every stage because there are abilities to utilize SSIS to load an entire database and incrementally load only changed data into a database.  This allows for SSIS to be extremely flexible for use with how old data can be in the reporting database.  For more on loading data based only on changes made, read [Backup file contents with SSIS – INSERT/UPDATE Decisions][2].

SSIS has a minimal learning curve allowing it to be quickly utilized for these operations.  After the knowledge of SSIS increases, many more uses are found in other administrative areas allowing more value to be added.

**SQL Server Replication**

The last option that is covered today is Replication.  [Replication][3] is a, _next to real-time_ solution for reporting.  The ability to base the data movement off only specific tables, columns and filters of the actual data, allow replication to be a formidable option for a real-time need.

Replication can be setup quickly and easily given the available wizards in the Tools available with SQL Server.  However, replication is a more complex feature that requires a much higher percentage of administration than other available options.  This can cause the initial setup of replication to be smooth, but later maintenance of it to be time-consuming. 

Limitations also arise to what must change on the publishing database in order for replication to function correctly.  In some vendor provided databases, altering tables can void support agreements and in some drastic cases, cause an application to stop functioning correctly. 

For more in-depth analysis and pros / cons on replication for reporting uses, read [Data Warehousing and Reporting][4] from BOL.

**What is the cost?**

This article has been based off the built in, ready to use options that SQL Server has available for offloading data for reporting needs.  So far we have not covered any costs that go along with those options.  To factor cost into any of these options, physical resources should be evaluated.

It is always recommended that offloaded data should reside on a separate physical SQL Server.  The server data offloaded should also be configured for transactions that are in OLAP nature.  This requires a high read I/O configuration and high availability of memory (based on each unique businesses level of reporting).  The mirroring and snapshot configuration requires an Enterprise Edition license which is an added cost.

Cost will arise mostly from licensing and hardware.  Additional Standard or Enterprise licensing is required when adding the secondary instance of SQL Server.  Hardware should be purchased that can withstand the high level of processing that heavy reporting needs can cause.  All of these factors will cause decisions on the type of feature that is used to offload data for reporting.  Weigh each one to the business needs and future scalability of the data carefully before making a decision.  

 [1]: http://msdn.microsoft.com/en-us/library/ms189940.aspx
 [2]: /index.php/DataMgmt/DBAdmin/backup-file-contents-with-ssis
 [3]: http://msdn.microsoft.com/en-us/library/ms151706.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms151784.aspx