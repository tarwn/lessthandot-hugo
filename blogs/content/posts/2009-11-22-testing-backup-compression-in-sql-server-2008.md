---
title: Testing backup compression in SQL Server 2008
author: SQLDenis
type: post
date: 2009-11-22T16:15:40+00:00
excerpt: |
  I got a brand new SQL Server 2008 test server and decided to test backup compression. I picked 2 databases to do this test; the smaller database is 4.8GB in size the bigger database is about 44 GB in size.
  Let's start with the smaller database.
  First&hellip;
url: /index.php/datamgmt/dbadmin/testing-backup-compression-in-sql-server-2008/
views:
  - 13905
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin
tags:
  - backups
  - compression
  - sql server 2008

---
I got a brand new SQL Server 2008 test server and decided to test backup compression. I picked 2 databases to do this test; the smaller database is 4.8GB in size the bigger database is about 44 GB in size.
  
Let&#8217;s start with the smaller database.
  
First I backed the database up, one backup used compression while the other one did not.

Let&#8217;s look at some code

<pre>BACKUP DATABASE [SmallDB] TO  DISK = N'V:SmallDB_Compressed.BAK' 
WITH NOFORMAT, NOINIT,  
NAME = N'SmallDB-Full Database Backup', SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  
STATS = 2
GO</pre>

The compressed backup took 34.313 seconds (138.881 MB/sec) to complete.

<pre>BACKUP DATABASE [SmallDB] TO  DISK = N'V:SmallDB_UnCompressed.BAK' 
WITH NOFORMAT, NOINIT,  
NAME = N'SmallDB-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,   
STATS = 2
GO</pre>

The uncompressed backup took 41.716 seconds (114.235 MB/sec) to complete.

As you can see the compressed backup was a little faster. The size of the backup was 1.25 GB for the compressed backup and 4.8 GB for the uncompressed backup

After I did the backups I decided to do the restores.

<pre>RESTORE DATABASE [SmallDB] 
FROM  DISK = N'V:SmallDB_Compressed.BAK' 
WITH  FILE = 1,  NOUNLOAD,  REPLACE,  STATS = 1
GO</pre>

RESTORE DATABASE successfully processed 609980 pages in 23.524 seconds (202.578 MB/sec).

<pre>RESTORE DATABASE [SmallDB] 
FROM  DISK = N'V:SmallDB_UnCompressed.BAK' 
WITH  FILE = 1,  NOUNLOAD,  REPLACE,  STATS = 1
GO</pre>

RESTORE DATABASE successfully processed 609977 pages in 38.951 seconds (122.344 MB/sec).

The restores had a bigger difference in time than the backups.

After I was done with the small database I decided to take a bigger database, this database is 44 GB, I wanted to see if using a bigger database would make a bigger or a smaller difference when using compressed backups compared to uncompressed backups.

<pre>BACKUP DATABASE MediumDB TO  DISK = N'V:MediumDB_Compressed.BAK' 
WITH NOFORMAT, NOINIT,  
NAME = N'MediumDB-Full Database Backup', SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  
STATS = 2
GO</pre>

The compressed backup took 303.113 seconds (142.845 MB/sec) to complete.

<pre>BACKUP DATABASE MediumDB TO  DISK = N'V:MediumDB_UnCompressed.BAK' 
WITH NOFORMAT, NOINIT,  
NAME = N'MediumDB-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,   
STATS = 2
GO</pre>

The uncompressed backup took 395.740 seconds (109.410 MB/sec) to complete.

Just as before with the small database, the compressed backup was a little faster. The size of the backup was 9.725 GB for the compressed backup and 44.338 GB for the uncompressed backup

Now let&#8217;s take a look at the restore.

<pre>RESTORE DATABASE [MediumDB] 
FROM  DISK = N'V:MediumDB_Compressed.BAK' 
WITH  FILE = 1,  NOUNLOAD,  REPLACE,  STATS = 1
GO</pre>

RESTORE DATABASE successfully processed 5542183 pages in 215.319 seconds (201.089 MB/sec).

<pre>RESTORE DATABASE [MediumDB] 
FROM  DISK = N'V:MediumDB_UnCompressed.BAK' 
WITH  FILE = 1,  NOUNLOAD,  REPLACE,  STATS = 1
GO</pre>

RESTORE DATABASE successfully processed 5542177 pages in 456.590 seconds (94.829 MB/sec)

The restore of the compressed backup took less than half the time of the uncompressed backup

To sum it all up:

  1. Backups are faster if you use compression
  2. Restore are significantly faster if you restore from a compressed backup
  3. Since the files are small you can store a lot more backups on the server and if you have to move it to another server it will be much faster also.

Another good thing is that with SQL Server 2008 R2 backup compression will be available in the standard edition as well, until now backup compression was only an Enterprise Edition feature

All in all I was impressed with backup compression and I can&#8217;t see a reason why you would not want to use it. It does use more CPU to backup when using compression but I am more RAM bound than CPU bound.



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22