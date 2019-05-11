---
title: Transaction Log expanding on restore after shrink
author: Ted Krueger (onpnt)
type: post
date: 2010-05-27T09:54:56+00:00
ID: 799
excerpt: 'The topic really does cause pain.  Shrinking a file in SQL Server is inherently a terrible action to take.  However, we all know that in some cases when maintenance was never setup and recovery models were not properly chosen, the need does come up.  An interesting topic came up on LTD today regarding moving an overloaded and unmaintained log file to a server that had less disk space than the original.  The first inclination would be to shrink the log and then backup/restore it to the new location.  Seeing as the full backup consists of the data and just enough log to recover, the assumption would be that you would only get this in the new database.  However, in this case, shrinking the log will not be the last step in the process.  The initial size of the log will also need to change.'
url: /index.php/datamgmt/dbprogramming/transaction-log-size-cold-shrink-ldf/
views:
  - 10122
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - backup
  - ldf
  - restore
  - shrinkfile
  - sql server
  - sql server 2008
  - transaction log

---
## My favorite topic, "The SHRINK!"

The topic really does cause pain. Shrinking a file in SQL Server is inherently a terrible action to take. However, we all know that in some cases when maintenance was never setup and recovery models were not properly chosen, the need does come up. An interesting [topic][1] came up on LTD in the forums regarding moving an overloaded and unmaintained log file to a server that had less disk space than the original. The first inclination would be to shrink the log and then backup/restore it to the new location. Seeing as the full backup consists of the data and just enough log to recover, the assumption would be that you would only get this in the new database. However, in this case, shrinking the log will not be the last step in the process. The initial size of the log will also need to change. 

## Let's take a look...

In this case let's say we have a database that is 300MB with a transaction log that is 3000MB. 

The database is a test log shipping database we will pick on named, "LOGSHIP_PUB"

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/shrunk_1.gif" alt="" title="" width="628" height="134" />
</div>

We can see by Size that we have the 300MB mdf and 3000MB log file. 

Now assuming we ran a shrinkfile on the log file, we could use DBCC SQLPERF(logspace) to check the logs free space. 

```sql
create table #temp (DatabaseName sysname, LogSize float, SpaceUsedPerc float, Status bit)
insert #temp EXEC ('dbcc sqlperf(logspace)')
select SpaceUsedPerc from #temp where DatabaseName = 'LOGSHIP_PUB'
```

Resulting in 0.028... give or take a few thousands places.

The initial thought may be to run a backup now and move the database over. You should see if you run a full backup on the database, the size will only be around 2MB. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/shrunk_2.gif" alt="" title="" width="594" height="27" />
</div>

The problem we will run into will be when this backup is restored. Even knowing the backup shows the 2MB, this is only the data and log to the point of recovering retained in the backup. You may find yourself copying this backup considering the restore to be limited to what is in it. What will happen is the restore will take the Meta for the database and create the initial sizes for the files contained in it. This will result in a required space of all the files initial sizes. In our case that totals 3300MB + the padding required. 

Best way to see is by example so let's restore the database.

```sql
RESTORE DATABASE [LOGSHIP_PUB_SECONDARY] FROM  DISK = N'C:LOGSHIP_PUB_FULL.bak' 
	WITH  FILE = 1,  
	MOVE N'LOGSHIP_PUB' TO N'C:LOGSHIP_PUB_SECONDARY.mdf',  
	MOVE N'LOGSHIP_PUB_log' TO N'C:LOGSHIP_PUB_SECONDARY_1.ldf',  
	NOUNLOAD,  REPLACE,  STATS = 10
GO
```

First thing we may notice is the time this restore takes. It will be slightly more than expected from a 300MB database. Your typical IO should give this in around a 2-3 seconds but given the need to expand the log(s) and any other files that may consist in the restore, the time will be lengthened slightly. Of course this varies given different resources and even more when compression is involved.
  
Our successful restore...

> SQL Server parse and compile time:
     
> CPU time = 0 ms, elapsed time = 1 ms.
  
> 63 percent processed.
  
> 100 percent processed.
  
> Processed 200 pages for database 'LOGSHIP\_PUB\_SECONDARY', file 'LOGSHIP_PUB' on file 1.
  
> Processed 1 pages for database 'LOGSHIP\_PUB\_SECONDARY', file 'LOGSHIP\_PUB\_log' on file 1.
  
> RESTORE DATABASE successfully processed 201 pages in 93.555 seconds (0.016 MB/sec).

Checking our size and space utilized we should see the same results from the initial database

```sql
Select [size],[name],[filename] from sysaltfiles
```
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/shrunk_3.gif" alt="" title="" width="543" height="50" />
</div>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/shrunk_4.gif" alt="" title="" width="628" height="141" />
</div></p> 

## So the bottom-line...

Moving databases using backup/restore is a good method if your activity allows for this method. Just ensure a few things

  * If log maintenance was not performed and you are stuck in Full recovery while finding the need for a shrink, you can do this successfully without a truncate_only but don't forget to resize your files.
  * Do the full backup after the resize to prevent longer restores and maintaining the disk usage you really require.
  * A well maintained transaction log structure will prevent all of this. You will know precisely the sizing requirements at this level.

 [1]: http://forum.ltd.local/viewtopic.php?f=17&t=10917