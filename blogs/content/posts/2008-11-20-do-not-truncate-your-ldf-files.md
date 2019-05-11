---
title: Do not truncate your ldf files!
author: Ted Krueger (onpnt)
type: post
date: 2008-11-20T14:24:20+00:00
ID: 212
excerpt: "You're the perfect DBA.  You have a small environement but critical none the less.  You've setup your disaster recovery plans based on a backup schedule and solid plan.  You've gone as far as to copy backups to external disk along with the tape backups&hellip;"
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/do-not-truncate-your-ldf-files/
views:
  - 42936
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
You're the perfect DBA. You have a small environement but critical none the less. You've setup your disaster recovery plans based on a backup schedule and solid plan. You've gone as far as to copy backups to external disk along with the tape backups the server administrators take nightly. Logs are backed up every 15 minutes just to make sure you can recover from anything including corrupt data and the largest of all fall outs. This morning however you saw your 70GB database all the sudden has a log size of 11GB and you're running out of space on the 36 drive you allocated for the logs. What's the first thing you do? Google regain disk space log files sql server. Sweet, I have a billion hits and scripts to truncate my log and all my space will be recovered. In particular you find something like this, "BACKUP LOG db\_name WITH truncate\_only". Happy DBA!!!

An hour later lightning hits the building completely taking everything out because the server administrators UPS system was fried along with everything else. The company will survive though as long as you can get power and the primary db server isn't fried also or grab that spare DL380 you have just for this case. See, you've even thought of that! Good DBA. So you install the OS and slam the drives into the spare server. You connect the external disk you made those copies of the backups on. Got it all going and feeling good about yourself. Smiling happy boss is all comfortable knowing they hired you for just this event. You restore your full backup and everything is good. Restore the differential the night before and then go to the logs. Funny thing is you notice there isn't a normal log that was backed up at around the time right before the disaster that is typically over 3GB. Then you remember this is the mission critical data import that takes place daily and is unrecoverable for more loads than the single one that takes place. No worries, it must be a smaller load than normal. You have a solid backup plan and they ahve to be in there. You restore the log right before you ran that disk saving DBCC command and then go to the next one.......................right about now you're heart is pumping so hard it feels like it will your chest is going to explode. What happened??? I don't have the data I thought and my logs are nothing more than 2K after the DBCC along with my job isn't all that safe right now.

Let's talk about what happen. There is this thing backups use called the LSN (Log Sequence Number). Very cirtical to the manner in which you can restore backups. In short when you truncated the log you essentially killed the LSN and broken the chain between the Full which is required for any differential or log backup to restore correctly. If you had access to your SQL Agent history for the job you would probably see an error for the log backup job after you truncated the logs stating something like, "BACKUP LOG cannot be performed because there is no current database backup". 

So lets watch it happen

create a db 

```sql
CREATE DATABASE [dr_db] ON PRIMARY 
( NAME = N'dr_db', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL10.LKF00TKSQL08MSSQLDATAdr_db.mdf' , SIZE = 3072KB , MAXSIZE = UNLIMITED, FILEGROWTH = 1024KB )
LOG ON 
( NAME = N'dr_db_log', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL10.LKF00TKSQL08MSSQLDATAdr_db_log.ldf' , SIZE = 1024KB , MAXSIZE = 102400KB , FILEGROWTH = 1%)
GO
```
Here is what we'll do then. Run these line for line

```sql
BACKUP DATABASE dr_db TO DISK='D:dr_db_full.bak' WITH INIT
BACKUP LOG dr_db TO DISK='D:db_dr_log1.trn'
CREATE TABLE dr_db.dbo.tbl (col1 int)
INSERT INTO dr_db.dbo.tbl VALUES (1)
BACKUP DATABASE dr_db TO DISK='D:dr_db_diff1.bak' WITH INIT,DIFFERENTIAL
INSERT INTO dr_db.dbo.tbl VALUES (2)
INSERT INTO dr_db.dbo.tbl VALUES (3)
BACKUP LOG dr_db TO DISK='D:db_dr_log2.trn'
INSERT INTO dr_db.dbo.tbl VALUES (4)
INSERT INTO dr_db.dbo.tbl VALUES (5)
INSERT INTO dr_db.dbo.tbl VALUES (6)
INSERT INTO dr_db.dbo.tbl VALUES (7)
BACKUP LOG dr_db WITH truncate_only
BACKUP DATABASE dr_db TO DISK='D:dr_db_diff2.bak' WITH INIT,DIFFERENTIAL
BACKUP LOG dr_db TO DISK='D:db_dr_log4.trn'
INSERT INTO dr_db.dbo.tbl VALUES (8)
INSERT INTO dr_db.dbo.tbl VALUES (9)
INSERT INTO dr_db.dbo.tbl VALUES (10)
INSERT INTO dr_db.dbo.tbl VALUES (11)
BACKUP LOG dr_db TO DISK='D:db_dr_log5.trn'
```
Pretty straight forward. You see the error come up right after the truncate\_only. What you've done is basically kill your recovery to any point in time after the log backup just prior to the truncate\_only. Luckily this is gone in SQL Server 2008 because you have no idea how many DBAs did this.

There is an option to get some disk back

If you are in need of regaining space run the SHRINKFILE on the log. If it does not shrink DO NOT TRUNC it unless you have no recovery plan. Then you're in more trouble than anyone can help so go ahead and trunc away 😉

You can see a shrinkfile in the same situation below work out as such

Same database and we'll create another named dr_db2

```sql
BACKUP DATABASE dr_db TO DISK='D:dr_db_full.bak' WITH INIT
BACKUP LOG dr_db TO DISK='D:db_dr_log1.trn'
CREATE TABLE dr_db.dbo.tbl (col1 int)
INSERT INTO dr_db.dbo.tbl VALUES (1)
BACKUP DATABASE dr_db TO DISK='D:dr_db_diff1.bak' WITH INIT,DIFFERENTIAL
INSERT INTO dr_db.dbo.tbl VALUES (2)
INSERT INTO dr_db.dbo.tbl VALUES (3)
BACKUP LOG dr_db TO DISK='D:db_dr_log2.trn'
INSERT INTO dr_db.dbo.tbl VALUES (4)
INSERT INTO dr_db.dbo.tbl VALUES (5)
INSERT INTO dr_db.dbo.tbl VALUES (6)
INSERT INTO dr_db.dbo.tbl VALUES (7)
DBCC SHRINKFILE ('dr_db_log',TRUNCATEONLY)
BACKUP LOG dr_db TO DISK='D:db_dr_log4.trn'
INSERT INTO dr_db.dbo.tbl VALUES (8)
INSERT INTO dr_db.dbo.tbl VALUES (9)
INSERT INTO dr_db.dbo.tbl VALUES (10)
INSERT INTO dr_db.dbo.tbl VALUES (11)
BACKUP LOG dr_db TO DISK='D:db_dr_log5.trn'
```
Now let's see a RESTORE

```sql
RESTORE DATABASE [dr_db2] FROM DISK = N'D:dr_db_full.bak' WITH FILE = 1, MOVE N'dr_db' TO N'D:SQLDATASQLSYSDATAMSSQL.1MSSQLDATAdr_db2.mdf', MOVE N'dr_db_log' TO N'D:SQLDATASQLSYSDATAMSSQL.1MSSQLDATAdr_db2_log.ldf', NORECOVERY, NOUNLOAD, REPLACE, STATS = 10
RESTORE DATABASE [dr_db2] FROM DISK = N'D:dr_db_diff1.bak' WITH NORECOVERY
RESTORE LOG [dr_db2] FROM DISK = N'D:db_dr_log2.trn' WITH NORECOVERY

RESTORE LOG [dr_db2] FROM DISK = N'D:db_dr_log4.trn' WITH NORECOVERY
RESTORE LOG [dr_db2] FROM DISK = N'D:db_dr_log5.trn' WITH NORECOVERY
RESTORE DATABASE dr_db2 WITH RECOVERY
```
Let's test it to see if my "tbl" table has data from 1 to 11

```sql
SELECT * FROM tbl
```
And it does 🙂 Now you're a happy DBA

Also if you have log shipping setup and you trunc that gets fun. Next entry I want to go into is using the COPY_ONLY which is based on another LSN topic while having log shipping and remote/local recovery points. This landscape is fun to setup and it may give you ideas on how you can quickly recover data and or from a disaster.

Rememeber, truncate very BAD!!!

Here is a resource for you to keep going on LSN and how backups work. This is important and you should know it if you ever typed the BACKUP command. Rmemeber, just because you can do something doesn't mean you should be doing it.

Backupset (viewing backups and LSN data) http://technet.microsoft.com/en-us/library/ms186299.aspx