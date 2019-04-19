---
title: SQL Server Filegroup Piecemeal Restores
author: Jes Borland
type: post
date: 2011-05-24T11:40:00+00:00
ID: 1187
excerpt: Learn how to use multiple filegroups to perform partial restores in a SQL Server database.
url: /index.php/datamgmt/dbadmin/sql-server-filegroup-piecemeal-restores-1/
views:
  - 145752
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
This is a follow-up to my previous post, [SQL Server Filegroups: The What, The Why and The How][1]. In that post, I described how you could create a database with multiple files spanning multiple filegroups, and how that can improve performance and administration. 

In this post, I'm going to show you another benefit of creating databases with multiple filegroups: the ability to restore one filegroup at a time, allowing part of the database to be accessible while the rest is being restored. This is known as a piecemeal restore. 

> Note: This feature is only available in SQL Server Enterprise Edition.

**The Scenario** 

I am going to create a database with four filegroups: Primary, FGFull2, FGFull3, and FGFull4. The Primary filegroup will be used only for system information. FGFull2 will be the default, and will contain order information from 2011. FGFull3 will contain order information from 2010. FGFull4 will contain order information from 2009, and will be read-only. 

I will show you how to create the database with multiple filegroups, and how to set options like the default and read-only. Then, I'll pretend I broke the database, and show you how to restore individual filegroups. 

Ready? Grab a cup of coffee, and let's go. 

**Creating and Populating the Database** 

First, I'll create a database with three filegroups, and one file per filegroup. 

```sql
USE master;
GO
CREATE DATABASE FilegroupFull ON PRIMARY
(NAME = FGFull1_dat,
 FILENAME = 'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLDATAFGFull1_dat.mdf'),
FILEGROUP FGFullFG2
(NAME = FGFull2_dat,
 FILENAME = 'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLDATAFGFull2_dat.mdf'), 
FILEGROUP FGFullFG3 
 (NAME = FGFull3_dat,
 FILENAME = 'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLDATAFGFull3_dat.mdf') 
LOG ON
(NAME = FGFull_log,
 FILENAME = 'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLDATAFGFull_log.ldf')
```

I want to make sure the database is in FULL recovery model, so I can take transaction log backups and restore them. 

```sql
ALTER DATABASE FilegroupFull SET RECOVERY FULL;
GO
```

You can easily add another filegroup and file to an existing database. I'm going to add a fourth. 

```sql
ALTER DATABASE FilegroupFull ADD FILEGROUP FGFullFG4 
ALTER DATABASE FilegroupFull ADD FILE
(NAME = FGFull4_dat,
 FILENAME = 'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLDATAFGFull4_dat.mdf')
TO FILEGROUP FGFullFG4
```

When an object is created in a database, it will be created in the DEFAULT filegroup. Initially, the filegroup PRIMARY is DEFAULT. I am going to change this so FGFullFG2 is DEFAULT. 

```sql
ALTER DATABASE FilegroupFull
MODIFY FILEGROUP FGFullFG2 DEFAULT
```

To view my filegroups and files, I can query sys.filegroups. 

```sql
USE FilegroupFull;
GO
SELECT name, data_space_id, type, type_desc, is_default, filegroup_guid, log_filegroup_id, is_read_only
FROM sys.filegroups;
```
<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/Filegroup1.JPG?mtime=1306200506"><img alt="" src="/wp-content/uploads/users/grrlgeek/Filegroup1.JPG?mtime=1306200506" width="805" height="131" /></a>
</div>

I can also access this information by right-clicking the database name in Object Explorer, selecting Properties, and then selecting Filegroups on the left. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/Filegroup2.JPG?mtime=1306200507"><img alt="" src="/wp-content/uploads/users/grrlgeek/Filegroup2.JPG?mtime=1306200507" width="699" height="625" /></a>
</div>

In your databases, you probably have Important Data that is accessed most frequently. This may be customer information, recent orders, or current stock. Your less-important data may be historical sales information, or a table listing ledger accounts in your accounting system. In the event of a disaster, during the recovery period, wouldn't it be great to be able to get the 20% of the data that is used 80% of the time online and usable first, and then work on restoring the remaining data while the business continues to function? How long will it take to restore a filegroup with, say 20 GB of data, instead of 120 GB? With some advance planning, this is possible. 

Next is creating tables and adding some orders. I am creating three tables. One will have orders for 2011, the second will have orders for 2010, and the third will be for 2009. This will be an example of how multiple filegroups can be useful, especially when combined with piecemeal restores.

First, I create the 2011 orders table. (If you are unfamiliar with CTEs, which I use to populate these tables, check out my blog post on [recursive CTEs][2]. 

```sql
USE FilegroupFull;
GO
CREATE TABLE Orders2011 
(OrderID INT NOT NULL, 
 OrderDate DATETIME NOT NULL 
 CONSTRAINT PKOrders PRIMARY KEY CLUSTERED (OrderID))
```

Let's check which filegroup this is created on. Will it be created on PRIMARY? 

```sql
SELECT PA.OBJECT_ID, FG.name
FROM sys.filegroups FG
    INNER JOIN sys.allocation_units AU ON AU.data_space_id = FG.data_space_id
    INNER JOIN sys.partitions PA ON PA.partition_id = AU.container_id
WHERE PA.OBJECT_ID =
    (SELECT OBJECT_ID(N'FilegroupFull.dbo.Orders2011'))
```
<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/Filegroup3.JPG?mtime=1306200507"><img alt="" src="/wp-content/uploads/users/grrlgeek/Filegroup3.JPG?mtime=1306200507" width="189" height="71" /></a>
</div>

Because I specified FGFullF2 as the default, that is where this table is created. 

Let's continue creating and populating tables. For my 2010 and 2009 tables, I will specify the filegroups I want them created on. 

```sql
CREATE TABLE Orders2010 
(OrderID INT NOT NULL, 
 OrderDate DATETIME NOT NULL 
 CONSTRAINT PKOrders2010 PRIMARY KEY CLUSTERED (OrderID)) 
ON FGFullFG3;
GO
 
;WITH InsertOrders (OrderID, OrderDate) AS 
(
SELECT 300100, 
	CAST('2010/05/01' AS DATETIME)
UNION ALL 
SELECT OrderID + 1, 
	DATEADD(dd, 1, OrderDate)
FROM InsertOrders 
WHERE DATEADD(dd, 1, OrderDate) <= DATEADD(dd, 100, '2010/05/01')
)
INSERT INTO Orders2010 
SELECT OrderID, OrderDate
FROM InsertOrders 
OPTION (MAXRECURSION 100);
CREATE TABLE Orders2009 
(OrderID INT NOT NULL, 
 OrderDate DATETIME NOT NULL 
 CONSTRAINT PKOrders2009 PRIMARY KEY CLUSTERED (OrderID)) 
ON FGFullFG4;
GO
 
;WITH InsertOrders (OrderID, OrderDate) AS 
(
SELECT 200100, 
	CAST('2009/05/01' AS DATETIME)
UNION ALL 
SELECT OrderID + 1, 
	DATEADD(dd, 1, OrderDate)
FROM InsertOrders 
WHERE DATEADD(dd, 1, OrderDate) <= DATEADD(dd, 100, '2009/05/01')
)
INSERT INTO Orders2009
SELECT OrderID, OrderDate
FROM InsertOrders 
OPTION (MAXRECURSION 100);
```

I'm going to mark FGFullFG4 as read-only, so I can show you the difference between restoring a read-write and a read-only filegroup. 

```sql
ALTER DATABASE FilegroupFull SET RESTRICTED_USER WITH ROLLBACK IMMEDIATE;
GO
ALTER DATABASE FilegroupFull MODIFY FILEGROUP FGFullFG4 READONLY;
GO
ALTER DATABASE FilegroupFull SET MULTI_USER;
GO
```

**Backing Up, and, More Importantly, Restoring** 

I will start with a full database backup. (You _are_ taking regular full backups of your databases, right?) 

```sql
BACKUP DATABASE FilegroupFull TO DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_full.bak'
```

Now, I'll add a record to my Orders2011 table. 

```sql
USE FilegroupFull;
GO
INSERT INTO Orders2011 
VALUES(400201, '2011/08/28');
```

Then, I perform a transaction log backup. (You _are_ performing regular transaction log backups of your databases in FULL and BULK_LOGGED recovery model, right?) 

```sql
BACKUP LOG FilegroupFull TO DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_tlog1.trn'
```

I'll add one more record to the table. 

```sql
INSERT INTO Orders2011 
VALUES(400202, '2011/08/29')
```

Then, I'll do one more step that you may need to, depending on what scenario you are faced with. I am going to back up the tail of the transaction log. 

```sql
BACKUP LOG FilegroupFull TO DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_tlogtail.trn' 
WITH NORECOVERY, NO_TRUNCATE;
```

Now, let's talk disaster. You should have, and regularly test, a disaster recovery process. I will simulate a disaster by dropping the database. Then, I will begin the restore. 

The first RESTORE statement in a piecemeal restore, which is known as a _partial-restore sequence_, must include the PRIMARY filegroup. It also must include the option WITH PARTIAL. You can restore as many filegroups as you want in the first restore. This is when testing your backups regularly comes into play. You should know how large your filegroups are, and how long they will take to restore, and how many you can restore at one time, to meet your company's disaster recovery business requirements. 

I'm going to begin with a restore of PRIMARY only. Because this database is in FULL recovery mode, I will need to restore the logs with every filegroup that is in read-write mode. 

```sql
RESTORE DATABASE FilegroupFull 
FILEGROUP = 'Primary'
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_full.bak' 
WITH PARTIAL, NORECOVERY 
RESTORE LOG FilegroupFull 
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_tlog1.trn' 
WITH NORECOVERY 
RESTORE LOG FilegroupFull 
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_tlogtail.trn' 
WITH RECOVERY;
GO
```

I can check the status of each file by querying sys.database_files. 

```sql
SELECT [name], [state_desc] 
FROM FilegroupFull.sys.database_files;
GO
```
<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/Filegroup4.JPG?mtime=1306200507"><img alt="" src="/wp-content/uploads/users/grrlgeek/Filegroup4.JPG?mtime=1306200507" width="269" height="141" /></a>
</div>

At this point, what will happen if I try to select from the Orders2011 table? 

```sql
USE FilegroupFull;
GO
SELECT OrderID, OrderDate
FROM Orders2011 
WHERE OrderID = 400189
```

<pre><span class="MT_red">Msg 8653, Level 16, State 1, Line 1
The query processor is unable to produce a plan for the table or view 'Orders2011'
because the table resides in a filegroup which is not online.</span></pre>

I continue with a restore of the FGFullFG2 filegroup. This is a _filegroup-restore sequence_. 

```sql
USE master;
GO
RESTORE DATABASE FilegroupFull 
FILEGROUP = 'FGFullFG2'
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_full.bak' 
WITH NORECOVERY
RESTORE LOG FilegroupFull 
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_tlog1.trn' 
WITH NORECOVERY 
RESTORE LOG FilegroupFull 
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_tlogtail.trn' 
WITH RECOVERY;
GO
```

Now, I am able to select from the Orders2011 table. 

```sql
USE FilegroupFull;
GO
SELECT OrderID, OrderDate
FROM Orders2011 
WHERE OrderID = 400189
```
<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/Filegroup5.JPG?mtime=1306200508"><img alt="" src="/wp-content/uploads/users/grrlgeek/Filegroup5.JPG?mtime=1306200508" width="237" height="69" /></a>
</div>

I am also able to INSERT. 

```sql
INSERT INTO Orders2011 
VALUES(400203, '2011/08/30')
```
<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/Filegroup6.JPG?mtime=1306200508"><img alt="" src="/wp-content/uploads/users/grrlgeek/Filegroup6.JPG?mtime=1306200508" width="225" height="67" /></a>
</div>

At this point, my PRIMARY and default filegroups are online. Users could begin working with objects in those filegroups. Any data stored on the files in RECOVERY_PENDING status will be unavailable until those filegroup restores are complete. 

Next up is a restore of FGFullFG3. This will bring the Orders2010 table online. 

```sql
USE master;
GO
RESTORE DATABASE FilegroupFull 
FILEGROUP = 'FGFullFG3'
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_full.bak' 
WITH NORECOVERY
RESTORE LOG FilegroupFull 
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_tlog1.trn' 
WITH NORECOVERY 
RESTORE LOG FilegroupFull 
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_tlogtail.trn' 
WITH RECOVERY;
GO 
```

Last but not least, I will restore FGFullFG4. Remember, this filegroup is in read-only. Because of this, I will only need to restore from the full backup. I do not need to restore any of the log files. 

```sql
USE master;
GO
RESTORE DATABASE FilegroupFull 
FILEGROUP = 'FGFullFG4'
FROM DISK = N'C:Program FilesMicrosoft SQL ServerMSSQL10_50.MSSQLSERVERMSSQLBackupFilegroupFull_full.bak' 
WITH RECOVERY;
GO
```

To test this, I will select from the Orders2009 table. 

```sql
USE FilegroupFull;
GO
SELECT OrderID, OrderDate
FROM Orders2009 
WHERE OrderID = 200190
```
<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/Filegroup7.JPG?mtime=1306200508"><img alt="" src="/wp-content/uploads/users/grrlgeek/Filegroup7.JPG?mtime=1306200508" width="234" height="62" /></a>
</div>

Success! 

At this point, all of my filegroups are online, and files and objects are accessible. So, my database is fully functional. Users have been able to access the database since first filegroup was brought online, and were able to complete at least some work while I continued to restore the files. 

**Think About It!** 

You should always be looking for ways to improve your database performance, simplify administration, and benefit the business. Using multiple filegroups may be one way to do all of this in your environment. Consider the possibilities! 

**REFERENCES**

Performing Piecemeal Restores (<http://msdn.microsoft.com/en-us/library/ms177425.aspx>)

Advanced BACKUP and RESTORE Options, Paul Randal (<http://www.sqlmag.com/article/database-backup-and-recovery/advanced-backup-and-restore-options-129834>)

 [1]: /index.php/DataMgmt/DBAdmin/sql-server-filegroups-the-what
 [2]: /index.php/DataMgmt/DBProgramming/MSSQLServer/t-sql-tuesday-18-a