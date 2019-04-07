---
title: Getting the physical device name and backup time for a SQL Server database
author: SQLDenis
type: post
date: 2010-04-02T09:22:20+00:00
ID: 733
url: /index.php/datamgmt/datadesign/getting-the-physical-device-name-and-bac/
views:
  - 12582
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - backup
  - recovery
  - sql server 2005
  - sql server 2008

---
There was a need to create a job that would take the last backup from one server and restore it on another server every day at 5 AM. There are a couple of things you need to know before you do that; some of these things are: permissions, what is the file name of the last backup, are there any jobs that need to be stopped and disabled before you restore etc etc.

This post will focus on getting the physical name for the last backup for a database. Let&#8217;s look at some code.

Create a test database

sql
CREATE DATABASE test
GO
```
Create some sample data

sql
USE test
GO

SELECT * INTO TestTable
FROM master..spt_values
GO
```
Now it is time to do the backups, I have a C:Junkdraw folder on my machine and a job that runs every night that will delete everything older than 2 weeks (I got this idea from the [lifehacks][1] book and it really helps with getting rid of not needed files). If you want to run the backup commands on your PC, just make sure to change C:Junkdraw to something that does exist.

sql
BACKUP DATABASE [Test] TO  DISK = N'C:JunkdrawTest.bak' WITH NOFORMAT, NOINIT
,SKIP, NOREWIND, NOUNLOAD,  STATS = 10
GO

BACKUP DATABASE [Test] TO  DISK = N'C:JunkdrawTest2.bak' WITH NOFORMAT, NOINIT
,SKIP, NOREWIND, NOUNLOAD,  STATS = 10
GO
```

In order to get the information about the backups we need to join the backupset and backupmediafamily tables. Here is the query that will give me the physical device name, backup start date, backup finish date and the size of the backup.

sql
SELECT			physical_device_name,
				backup_start_date,
				backup_finish_date,
				backup_size/1024.0 AS BackupSizeKB
FROM msdb.dbo.backupset b
JOIN msdb.dbo.backupmediafamily m ON b.media_set_id = m.media_set_id
WHERE database_name = 'Test'
ORDER BY backup_finish_date DESC
```

Output

<pre><strong>physical_device_name	backup_start_date	backup_finish_date	BackupSizeKB</strong>
C:JunkdrawTest2.bak	2010-03-24 12:43:43.000	2010-03-24 12:43:43.000	1483.000000
C:JunkdrawTest.bak	2010-03-24 12:43:33.000	2010-03-24 12:43:33.000	1482.000000</pre>

What if you want to know the time the last backup finished for all the databases? You can use the following query to get that info

sql
WITH LastBackupTaken AS (
SELECT database_name,
backup_finish_date,
RowNumber = ROW_NUMBER() OVER (PARTITION BY database_name 
							ORDER BY backup_finish_date DESC)
FROM msdb.dbo.backupset 

)

SELECT database_name,backup_finish_date 
FROM LastBackupTaken
WHERE RowNumber = 1
```

Here are the results for that query

<pre>master	2010-03-24 12:58:03.000
model	2010-03-24 12:58:24.000
msdb	2010-03-24 12:58:15.000
Policy	2010-03-24 09:59:32.000
Test	2010-03-24 12:43:43.000</pre>

This last part is also on our wiki, you can find it here [Get last backup dates for all databases &#8206;][2]

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://www.amazon.com/gp/product/0470238364?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=0470238364
 [2]: http://wiki.ltd.local/index.php/Get_last_backup_dates_for_all_databases
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22