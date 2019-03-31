---
title: Buffer Latch Error Restoring SQL Server Database
author: Ted Krueger (onpnt)
type: post
date: 2013-04-10T07:39:00+00:00
excerpt: 'When working on SQL Server that is stretching IO and the subsystem to its limits, you are bound to see a buffer latch time-out error at some point.  This is typically, "Time-out occurred while waiting for buffer latch type 2 or 3". If you do, it will us&hellip;'
url: /index.php/datamgmt/dbadmin/buffer-latch-error-restoring-sql/
views:
  - 24399
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
When working on SQL Server that is stretching IO and the subsystem to its limits, you are bound to see a buffer latch time-out error at some point. This is typically, &#8220;Time-out occurred while waiting for buffer latch type 2 or 3&#8221;. If you do, it will usually occur while you are performing a large maintenance task, integrity check or something that really impacts IO to a level that causes the buffer latch time-out. In a recent case, I ran into this during a restore operation of a database that was a few hundred GB. This usually is a fairly quick operation and one that doesn’t cause an IO issue with SQL Server but in this case, the restore was pushed to a single SSD drive. This was only due to the restore being temporary and the SSD drive was free to handle the task.

In this case, the restore statement was as simple as it can get.

<pre>USE [master]
RESTORE DATABASE TempRestore FROM  DISK = N'E:Backup DataTempRestore.bak' 
WITH  FILE = 1,  MOVE N'TempRestore' TO N'I:TempRestore.mdf',  MOVE N' TempRestore_log' TO N'I:TempRestore.ldf',  NOUNLOAD,  REPLACE,  STATS = 5
GO</pre>

The restore ran fine, as shown below, until it hit the database version upgrade section. In this case, this is a SQL Server 2008 database, restoring to SQL Server 2012. Or, database version 661 to 706.

5 percent processed.
  
10 percent processed.
  
15 percent processed.
  
20 percent processed.
  
25 percent processed.
  
30 percent processed.
  
35 percent processed.
  
40 percent processed.
  
45 percent processed.
  
50 percent processed.
  
55 percent processed.
  
60 percent processed.
  
65 percent processed.
  
70 percent processed.
  
75 percent processed.
  
80 percent processed.
  
85 percent processed.
  
90 percent processed.
  
95 percent processed.
  
100 percent processed.
  
Processed 532576 pages for database &#8216;TEMPRESTORE&#8217;, file &#8216;TEMPRESTORE&#8217; on file 1.
  
Processed 4 pages for database &#8216;TEMPRESTORE&#8217;, file &#8216;TEMPRESTORE_log&#8217; on file 1.
  
<span class="MT_red">Msg 3013, Level 16, State 1, Line 2<br /> RESTORE DATABASE is terminating abnormally.<br /> Msg 845, Level 17, State 1, Line 2<br /> Time-out occurred while waiting for buffer latch type 3 for page (1:0), database ID 27.</span></p> 

As shown, the error occurred upgrading the database and the restore failed. At this point, the database is left in restoring state or no recovery. What really isn’t shown is the fact the database restore was really done and the database upgrade was all that remained. This is where knowing restore stages and processing is a good piece of knowledge. (To learn more, &#8220;[Understanding How Restore and Recovery of Backups Work in SQL Server][1]&#8220;).

Since the database was restored successfully and the only part that remained was to bring it online, upgrading the internal database version, the following statement should resolve the problem without requiring a completely new restore execution. In most cases, a restore is a one-way street. Start a restore and if it errors, there is no other way but starting over. In this case, don’t be so quick to take that ultimatum.

<pre>RESTORE DATABASE TempRestore WITH RECOVERY
GO</pre>

The resulting messages below show the restore was at a state that allowed the call for recovery to complete and bring the database online by upgrading the internal version. Now, a critical step here to verify the databases integrity is to run a CHECKDB before allowing activity on it. This is even more critical if this is a restore that is required for production. Do not skip a CHECKDB on a database if this happens to you.

Converting database &#8216;TEMPRESTORE&#8217; from version 661 to the current version 706.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 661 to version 668.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 668 to version 669.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 669 to version 670.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 670 to version 671.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 671 to version 672.
  
………
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 700 to version 701.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 701 to version 702.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 702 to version 703.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 703 to version 704.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 704 to version 705.
  
Database &#8216;TEMPRESTORE&#8217; running the upgrade step from version 705 to version 706.
  
RESTORE DATABASE successfully processed 0 pages in 289.824 seconds (0.000 MB/sec).

**Summary**

 ****

If you run into the error, “Time-out occurred while waiting for buffer latch type 3 &#8230;” at the end of a restore, run a restore with recovery on the database before taking the long way around and running the entire restore again. If the recovery is successful, run a CHECKDB to ensure the integrity of the database is good and if it is, your error will not have caused you a much longer and time consuming task of running the entire restore over.

 [1]: http://msdn.microsoft.com/en-us/library/ms191455%28v=sql.105%29.aspx