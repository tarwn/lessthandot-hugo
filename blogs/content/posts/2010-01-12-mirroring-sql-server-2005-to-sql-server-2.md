---
title: Mirroring SQL Server 2005 to SQL Server 2008 (part 2)
author: Ted Krueger (onpnt)
type: post
date: 2010-01-12T16:24:02+00:00
ID: 669
url: /index.php/datamgmt/dbprogramming/mirroring-sql-server-2005-to-sql-server-2/
views:
  - 26756
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
## Review

This is an extension of [part 1][1] and the feasibility of mirroring a SQL Server 2005 Enterprise database to SQL Server 2008 Enterprise database in order to have a limited downtime upgrade path.

In this part, we're going to work through the actual test case and setup of the process.

I recommend this type of upgrade only if downtime is extremely limited in your installation. Remember to always backup your databases and all associated sql agent jobs, scripts, logins and objects before going through with a complete upgrade of SQL Server. My normal process on planning an upgrade is, if you spent an hour determining what needs to be done, you need to add another 5 hours validating your thoughts. 

For our lab, let's start by setting up the database and the mirror. The following steps we will go through will be almost identical to the steps we worked through together in a [previous exercise][2] to move databases between SQL Server instances of the same 2005 versions using mirroring. 

## Let's jump in

Create the database on the SQL Server 2005 instance, run a full backup and then a backup of the transaction log to capture the tail-end transactions for applying to the secondary database (mirror). 

sql
CREATE DATABASE NEEDTOUPGRADE ON  PRIMARY 
( NAME = N'NEEDTOUPGRADE', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOUPGRADE.mdf' , SIZE = 2048KB , MAXSIZE = UNLIMITED, FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'NEEDTOUPGRADE_log', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOUPGRADE_log.ldf' , SIZE = 1024KB , MAXSIZE = 2048GB , FILEGROWTH = 10%)
GO
--run initial full backup
BACKUP DATABASE NEEDTOUPGRADE TO DISK = 'C:NEEDTOUPGRADE_full_initial.bak'
--tail end log backup
BACKUP LOG NEEDTOUPGRADE TO DISK = 'C:NEEDTOUPGRADE_taillog_initial.trn'
```

Next, we'll use a basic endpoint configuration and start the endpoint to prepare it for the mirror.

sql
CREATE ENDPOINT [Mirroring] 
    AUTHORIZATION [PMHCtkrueger]
    STATE=STARTED
    AS TCP (LISTENER_PORT = 5022, LISTENER_IP = ALL)
    FOR DATA_MIRRORING (ROLE = PARTNER, AUTHENTICATION = WINDOWS NEGOTIATE
, ENCRYPTION = REQUIRED ALGORITHM RC4)
```
</p> 

Now let's move to the SQL Server 2008 instance and restore the database from our full backup. After this we can apply the log backup to bring us to up to the level we can initialize synchronization between the databases.

sql
--restore the full
RESTORE DATABASE NEEDTOUPGRADE 
FROM DISK = 'C:NEEDTOUPGRADE_full_initial.bak'
WITH NORECOVERY,
MOVE 'NEEDTOUPGRADE' TO N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOUPGRADE_mirror.mdf',
MOVE 'NEEDTOUPGRADE_log' TO N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOUPGRADE_mirror_log.ldf'
,REPLACE,NORECOVERY
GO
--apply the tail end log 
RESTORE LOG NEEDTOUPGRADE FROM DISK = 'C:NEEDTOUPGRADE_taillog_initial.trn' WITH NORECOVERY
GO
```

Now the database on SQL Server 2008 is in no recovery and we can start by configuring it for mirroring. We do this by setting the database as the partner based on communication coming through port 5022. 

> <span class="MT_red">Note: Our database is still version 611. SQL Server 2008 will be version 655. This prevents us from making normal snapshots and some other things we normally can do on a mirror. This was covered in some cons about this process in part 1.</span>

First, let's create the endpoint on the mirror (SQL Server 2008)

sql
CREATE ENDPOINT [Mirroring] 
    AUTHORIZATION [PMHCtkrueger]
    STATE=STARTED
    AS TCP (LISTENER_PORT = 5023, LISTENER_IP = ALL)
    FOR DATA_MIRRORING (ROLE = PARTNER, AUTHENTICATION = WINDOWS NEGOTIATE
, ENCRYPTION = REQUIRED ALGORITHM RC4)
```

Now configure the database so it will act as the partner

sql
ALTER DATABASE NEEDTOUPGRADE SET PARTNER= N'TCP://Servername:5022'
```

Once the mirror is ready to accept transactions, we can configure the principle.

Back on our SQL Server 2005 instance we simply can tell the NEEDTOUPGRADE database that it is the partner for port 5023. 

sql
ALTER DATABASE NEEDTOUPGRADE SET PARTNER= N'TCP://Servername:5023'
```

We're not using a witness so at this point we want to set safety off. While still on the principle SQL Server 2005 instance run the following alter database statement to turn safety off.

sql
ALTER DATABASE NEEDTOUPGRADE SET SAFETY OFF
```
</p> 

At this point we can see we are actively mirroring the database NEEDTOUPGRADE between a SQL Server 2005 and SQL Server 2008 instance. At this stage our theory and test case is successful. 

## Finishing the upgrade

To complete our upgrade, we need to fail the mirror over. Problems come into play here. Once you fail the mirror you will see the SQL Server 2008 database go into (Principle, Suspended) mode. This is because our mirroring is a one way mirror situation. In short, we can only apply the transactions from 2005 to 2008. This is where we need to stress backups being taken prior to making the failover. The database is fully functional even in suspended mirroring so if you have covered all the other aspects to an upgrade, you can validate the applications and allow the users back in. 

## Cleaning up

The database is in compatibility mode 90, version 611 and in principle and suspended mode and we want to clean this up. At this point in a real life scenario, the first task would be to have a new mirror available and ready to be configured for a normal landscape of 2008 to 2008. Next, if the application that uses your database allows for upgrading to compatibility mode 100, then I highly recommend it at this time. 

To remove mirroring, on the SQL Server 2008 instance run

sql
ALTER DATABASE NEEDTOUPGRADE SET PARTNER OFF
```

Removing mirroring accomplishes cleaning the upgrade mirror configuration and upgrades our database to version 655 for us. This does not remove endpoints though. You can verify the version change of the database with

sql
SELECT DATABASEPROPERTY('NEEDTOUPGRADE','version')
```

Next we can change to compatibility level 100 (SQL Server 2008) with the following 

sql
ALTER DATABASE NEEDTOUPGRADE SET COMPATIBILITY_LEVEL = 100
```
</p> 

Last step and most common will be to fix orphaned database logins that may appear in NEEDTOUGPRADE. This is the hardship for the user community typically and depending on the applications, passwords may need to be reset if you are under SOX regulations and using SQL Authentication. SOX regulations will not allow you access to user passwords so you will need to reset them and force them to change their password on the next login attempt. If you are using windows authentication then nothing but fixing the orphaned logins need to be accomplished. There is a script you can use to fix orphaned logins up on the wiki site of LessThanDot, [_Fix Orphaned Database Users_][3]
  

  
All of these steps we went through added up to about 5 or 10 minutes of actual downtime to the user community. This can be drastically lowered if you prepare well and have supporting objects like dataabse logins ready for the scripts to handle reconnecting them to server logins. The amount of logins and the need to manipulate passwords will lengthen this slightly. Other supporting objects, script, services and such that are required for the database will also be different from installation to installation. The one thing I recommend is planning and letting the users know well in advance so they can prepare for the upgrade. Even if the downtime is minutes and the your test cases show no loss of connectivity to the data store, you should prepare them and share the plans of the upgrade. Not only will letting your user community know of the upgrade in advance prepare them for when it will happen, it will make the move seem less lengthy to them and strengthen your relationship with them.

 [1]: /index.php/DataMgmt/DBAdmin/mirroring-sql-server-2005-to-sql-server-2008
 [2]: /index.php/DataMgmt/DBAdmin/move-databases-to-new-server-with-little-1
 [3]: http://wiki.ltd.local/index.php/Fix_Orphaned_Database_Users