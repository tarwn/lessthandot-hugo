---
title: Read data on Mirror to prevent production database stress
author: Ted Krueger (onpnt)
type: post
date: 2009-05-13T12:35:23+00:00
ID: 428
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/read-data-on-mirror-to-prevent-productio/
views:
  - 15076
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
Today my subscription from SQL Server Central had this for the question of the day, &#8220;Which of the following statements would you use to allow read-only access to the data and objects held within the mirrored copy of a mirrored database, without breaking the mirroring session?&#8221;

The answer is pretty easy but I noticed there was an alarming amount of answers that were wrong. Mostly saying an ALTER for READ_ONLY was the way to get the job done. I decided this would make a good write on how to actually do it. I needed a DB and a mirror of that DB first so here is how we get that set up.

Step one is to create a DB to actually work with. In our imaginary world this is going to be our principle that must not go offline, take on heavy load from DR methods and not have futile locks applied resulting in production slowdowns.

sql
CREATE DATABASE [PRINCIPLE_DB] 
```
Now to get a mirror up and running you have the basic 1,2,3 setup as I call it.

1. Backup the principle
  
2. Backup the tail end of the log on the principle
  
3. Restore to secondary with norecovery

There really is not much more to it than that to actually get a mirror up. The part that you need to use your brain on is the means in which you want to synchronize the mirror and the type of failover you are looking for. For this example I’m going to use asynchronous mirroring. I suggest that you read up on these types if you ahve not already. Refer to these links for a starting point 

http://msdn.microsoft.com/en-us/library/ms187110.aspx
  
http://msdn.microsoft.com/en-us/library/ms188712.aspx

Later in my writing on LTD I plan to do a lot of mirroring discussions and I hope to write something useful for everyone, but MSDN is of course always your accurate resource to get you going. 

First thing is to back the database up. The backup and restore steps are critical and probably the single most common place people have problems in getting a mirror started. Follow these steps to get the mirror into recovering mode and both in sync in order to start mirroring.

Backup script

sql
BACKUP DATABASE [PRINCIPLE_DB] 
TO DISK = N'C:principle_db_mirror_startup.bak' 
WITH FORMAT
```
To make it interesting and give us an objective to select on later, throw this in there after

sql
Use PRINCIPLE_DB
Go
CREATE TABLE TBL (ident INT Identity(1,1))
Go
INSERT INTO TBL
DEFAULT VALUES  
Go 10
```
Now sense we have control here and no one is doing anything to the test DB, backup the tail end of the log now.

sql
BACKUP LOG [PRINCIPLE_DB] 
TO DISK = 'C:principle_db_mirror_logstartup.trn' 
WITH FORMAT
```
Copy those over to your second instance in your lab setup.
  
Next is to restore the backups to the instance you want as the mirror. Here is the restore command for your second instance

sql
RESTORE DATABASE [PRINCIPLE_DB] FROM  DISK = N'C:principle_db_mirror_startup.bak' 
WITH  REPLACE,NORECOVERY,  
MOVE N'PRINCIPLE_DB' TO N'C:PRINCIPLE_DB.mdf',  
MOVE N'PRINCIPLE_DB_log' TO N'C:PRINCIPLE_DB_Log.LDF'
GO
```
And restore the log

sql
RESTORE LOG [PRINCIPLE_DB] 
FROM DISK = 'C:principle_db_mirror_logstartup.trn' 
WITH NORECOVERY
```
Getting the mirror going in its simplest form without really going into the configuration and planning strategy can be done with a short ALTER DATABASE script and setting the partner to the endpoints either you created or the default Mirroring endpoints. First thing is to make sure your endpoints are configured. You can query sys.endpoints or a bit more informative with sys.database\_mirroring\_endpoints. If your endpoints are not up the basic command to get one is

sql
CREATE ENDPOINT Mirroring
	STATE=STARTED
AS TCP (LISTENER_PORT=5022)
FOR DATABASE_MIRRORING (ROLE=PARTNER)  
GO
```
Running the query above on sys.database\_mirroring\_endpoints, you should see DATABASE_MIRRORING listed. If not, you can recreate the endpoint either by scripting it or use SSMS to start up your mirror and allow it to configure the endpoints. Assuming the endpoints are all up and configured, the script to start the mirror is 

On the Mirror first run

sql
ALTER DATABASE [PRINCIPLE_DB]
SET PARTNER = 'TCP://principle.fullyqualifiedname.com:5022'
GO
```
Then on the principle instance

sql
ALTER DATABASE [PRINCIPLE_DB]
SET PARTNER = 'TCP://mirror.fullyqualifiedname.com:5022'
GO
```
Mirroring should be going after this point. If you get local copy errors or endpoint errors you more than likely have either the RTM version running and need to start trace flag 1400 or your backup/restore wasn’t done correctly. Try to go through the steps again and see if the error is resolved.

Now we have a mirror to actually answer the question that was asked on SQL Server Central, we can actually answer it. Basically creating snapshots is the answer. A few important things to think about here are Enterprise edition is the only way you’re going to get what you need. Mirroring is available in standard but only as that. You cannot backup or read the mirror in standard. This is an Enterprise feature so plan for this when asking for licensing. Luckily, developer edition is identical to enterprise. This means for around $40 you can learn how to configure all the editions and use all the features.

On your mirror instance start a new query window in the master context.
  
Run the following

sql
CREATE DATABASE PRINCIPLE_DB_COPY
ON (NAME = 'PRINCIPLE_DB', FILENAME = 'C:PRINCPLE_DB_COPY.SNP')
   AS SNAPSHOT OF PRINCIPLE_DB
```
If you go under the Database Snapshots tree now in SSMS, you should see PRINCIPLE\_DB\_COPY. Do a &#8220;SELECT * FROM TBL&#8221; and we can now read our data off the mirror using snapshots in a read-only mode.