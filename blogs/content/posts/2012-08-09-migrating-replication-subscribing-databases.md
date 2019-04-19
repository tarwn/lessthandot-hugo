---
title: Migrating Replication Subscribing Databases
author: Ted Krueger (onpnt)
type: post
date: 2012-08-09T12:34:00+00:00
ID: 1691
excerpt: 'In a merge replicating world, laptops are typically the majority of the subscribing machines.  With laptops and any other user controlled computer, changes can come up - upgrades are needed or new hardware rollouts.  These all cause the need for a new o&hellip;'
url: /index.php/datamgmt/dbadmin/migrating-replication-subscribing-databases/
views:
  - 8982
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
In a merge replicating world, laptops are typically the majority of the subscribing machines.  With laptops and any other user controlled computer, changes can come up – upgrades are needed or new hardware rollouts.  These all cause the need for a new or rebuilt laptop / desktop to be issued to users.  Many times when the need for a rebuild or replacement happens and the machine was subscribing to a merge replication publication, the steps taken are to apply a new snapshot.  This is typically needed once a DBA calls for the need to reinitialize the subscriber off a new snapshot.  Although this method works, it is a massively time consuming process and isn't' needed.

**Replication and Domains** 

Replication functions on domain names more than anything.  You'll see this even when trying to monitor a replication server. When attempting to open replication monitor while connected to an instance via TCP/IP, you will be met by warnings and errors of requiring the fully qualified domain name in order to administer replication.

This aspect of replication is a great step in the task of moving subscribers around.  If a laptop or desktop does need to be rebuilt or replaced, making the new machine's name the same as the old allows the relationship between the publisher and the subscriber to continue without skipping a beat.

**Two machines enter, one machine leaves**

The hardship to this task is, two machines can't have the same name on the network at the same time.  Of course, if the old machine crashes due to HDD failures or such, a new snapshot is required. But, if the old machine is still functional to the point you can get the mdf and ldf files, you are sitting in a good situation.

Note: if you are unsure which mdf and ldf files are being used for the subscriber database, use the following query either in SSMS or SQLCMD to return the files and paths.

```sql
SELECT physical_name FROM sys.master_files WHERE name = '<db name>'
```


 

One way to accomplish this task is to use detach and attach in SQL Server.  First, detach the database that is the subscriber on the old machine.  If your database name is, SalesMan, the detach would appear as shown below.

```sql
EXEC sp_detach_db 'SalesMan', 'true';
```


 

Now, copy the mdf and ldf filed for the database to an external storage device.  This can be any type of storage – a USB drive, external HDD or network storage area.  Shut down the old machine and start the new machine or, if the new machine is already started and the name is different, rename the new machine to the same as the old machinee.  You will probably need to work with your domain admin to remove the machine from the domain before shutting the old machine down.

Once you've renamed the new machine to the same as the old machine, attach the database.  This is done by first copying the mdf and ldf into the directory on the machine where you want them to be located.  Then attach the database using the CREATE DATABASE statement.

```sql
USE [master]
GO
CREATE DATABASE [SalesMan] ON
( FILENAME = N'<path to mdf you copied>'),
( FILENAME = N'<path to ldf you copied>')
FOR ATTACH
GO
```


Now, replicate!  You should see replication function correctly.