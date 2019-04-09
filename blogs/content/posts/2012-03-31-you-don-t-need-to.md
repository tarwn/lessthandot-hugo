---
title: You don’t need to shrink a database to get a smaller backup
author: SQLDenis
type: post
date: 2012-03-31T16:23:00+00:00
ID: 1583
excerpt: 'Last weekend I decided to do some maintenance on one of our database to see if I can get some freespace back. I use compression for some of the older tables and also reindexed the tables with a higher fill factor. After I was done, I got over 200 GB of&hellip;'
url: /index.php/datamgmt/datadesign/you-don-t-need-to/
views:
  - 9511
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server Admin
tags:
  - how to
  - maintenance
  - sql server 2000
  - sql server 2005
  - sql server 2008

---
Last weekend I decided to do some maintenance on one of our database to see if I can get some freespace back. I use compression for some of the older tables and also reindexed the tables with a higher fill factor. After I was done, I got over 200 GB of additional free space

Here is what the database looked like before I did the maintenance

<pre>FILEID	FILE_SIZE_MB	SPACE_USED_MB	FREE_SPACE_MB
1	179353.81	162922.13	16431.69
2	    64.01	    14.33	   49.68
3	297089.13	265538.44	31550.69
4	344555.69	298126.69	46429.00
5	165258.50	123946.63	41311.88</pre>

Here is what the database looked like after I did the maintenance

<pre>FILEID	FILE_SIZE_MB	SPACE_USED_MB	FREE_SPACE_MB
1	179353.81	110124.50	 69229.31
2	  2085.63	   994.09	  1091.54
3	297089.13	259595.44	 37493.69
4	344555.69	169405.19	175150.50
5	186822.69	123962.00	 62860.69</pre>

As you can see I did nicely here, free space for fileid 4 went from 46 GB to 175 GB, for fileid 1 it went from 16 GB to 69 GB

Of course I had to brag about this and then it happened….the sentence you never want to hear…….how come you didn't shrink the database, the backups will still be as big…you are wasting space….if only I could send Paul Randal or Ted Krueger to this person…….

So let's debunk that myth shall we?

First create these 3 database, they will be 3 MB, 3 GB and 9 GB in size

sql
CREATE DATABASE [TestSmall]
 ON  PRIMARY 
( NAME = N'TestSmall', FILENAME = N'C:SQLFilesTestSmall.mdf' , SIZE = 3072KB , FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'TestSmall_log', FILENAME = N'C:SQLFilesTestSmall_log.ldf' , SIZE = 1024KB , FILEGROWTH = 10%)
GO



CREATE DATABASE [TestLarge]
 ON  PRIMARY 
( NAME = N'TestSmall', FILENAME = N'C:SQLFilesTestLarge.mdf' , SIZE = 3072MB , FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'TestSmall_log', FILENAME = N'C:SQLFilesTestLarge_log.ldf' , SIZE = 1024KB , FILEGROWTH = 10%)
GO



CREATE DATABASE [TestLarger]
 ON  PRIMARY 
( NAME = N'TestSmall', FILENAME = N'C:SQLFilesTestLarger.mdf' , SIZE = 9072MB , FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'TestSmall_log', FILENAME = N'C:SQLFilesTestLarger_log.ldf' , SIZE = 1024KB , FILEGROWTH = 10%)
GO
```

Here is what it looks like in file explorer

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/sqlfilesorig.PNG?mtime=1333217594"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/sqlfilesorig.PNG?mtime=1333217594" width="364" height="262" /></a>
</div>

Or if you want to do this from T-SQL

exec sp_helpdb 

<pre>name		db_size	    owner		dbid	created	
----------      --------    ---------------     ----    -----------
master		4.75 MB	    sa			1	Apr  8 2003	
TestLarge    3073.00 MB	    Denis-PCDenis	9	Mar 31 2012	
TestSmall	4.00 MB	    Denis-PCDenis	8	Mar 31 2012	
TestLarger   9073.00 MB	    Denis-PCDenis	10	Mar 31 2012</pre>

You can see that the files are indeed in the GB and in the MB range

Now back the database up, these are plain vanilla backups, no compression is applied

sql
BACKUP DATABASE [TestLarge] TO  DISK = N'D:SQLBackupsTestLarge.BAK' 
WITH NOFORMAT, NOINIT,  NAME = N'TestLarge-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,  STATS = 10
GO

BACKUP DATABASE [TestLarger] TO  DISK = N'D:SQLBackupsTestLarger.BAK' 
WITH NOFORMAT, NOINIT,  NAME = N'TestLarger-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,  STATS = 10
GO

BACKUP DATABASE [TestSmall] TO  DISK = N'D:SQLBackupsTestSmall.BAK' 
WITH NOFORMAT, NOINIT,  NAME = N'TestLarge-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,  STATS = 10
GO
```

Navigate to the folder and what do you see?

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/sqlfiles.PNG?mtime=1333217577"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/sqlfiles.PNG?mtime=1333217577" width="449" height="204" /></a>
</div>

That's right there is only MBs difference between the 3MB database and the 3 GB database. I checked my production server and the backup took 30 minutes less to complete compared to the backups that ran before I freed up space

I won't go into details here why shrinking the database is bad but I can guarantee you that it is no coincidence that shrink could mean _making the database smaller_ and _psychiatrist_ 🙂