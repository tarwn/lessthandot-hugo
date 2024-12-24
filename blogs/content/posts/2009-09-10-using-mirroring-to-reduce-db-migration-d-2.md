---
title: Using Mirroring to Reduce DB Migration Downtime (Part 2)
author: Ted Krueger (onpnt)
type: post
date: 2009-09-10T10:28:55+00:00
ID: 553
url: /index.php/datamgmt/dbprogramming/using-mirroring-to-reduce-db-migration-d-2/
views:
  - 15296
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
## _Part 1_

In the first part to this article we went over setting up the lab to show exactly how to perform a migration of databases to a new server with limited downtime. If you haven't read the first part, you can find it [here][1]. Together we went over how to create a mirror and prepare a test lab for utilizing this method. If you already have a mirror in your test or development landscape then you can use that for the follow up. If you need a test lab setup, part 1 will get you in the position to go through all the steps that will follow.

## _The Migration_

The steps for the actual preparation and migration

  * Run a full backup and transaction log backup on the principle
  * Restore our full backup and transaction log backup to the new server
  * Stop mirroring on the old mirror
  * Configure and start mirroring on the new server

First task for us to prepare the new server was to restore a full backup of the principle. Once this is done, as we did in part 1 with setting up our mirror, we will create a transaction log backup to capture the tail end of the log and restore it as well.
  
To run our full backup and transaction log backup run the following script

```sql
BACKUP DATABASE [NEEDTOMOVE] TO DISK = 'C:needtomove_full_migration.bak'
Go
BACKUP LOG [NEEDTOMOVE] TO DISK = 'C:needtomove_taillog_migration.trn'
Go
```
Once our backups are created we can move to the new server and restore them. As mentioned earlier in part 1 while setting up our initial mirror, the restore process will require the WITH NORECOVERY to place the database in recovering. 

To accomplish the full restore run the following statements on MYLABNEW

```sql
RESTORE DATABASE [NEEDTOMOVE] 
FROM DISK = 'C:needtomove_full_migration.bak'
WITH NORECOVERY,
MOVE 'NEEDTOMOVE' TO N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOMOVE_mirror.mdf',
MOVE 'NEEDTOMOVE_log' TO N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOMOVE_mirror_log.ldf'
,REPLACE,NORECOVERY
Go
RESTORE LOG [NEEDTOMOVE] FROM DISK = 'C:needtomove_taillog_migration.trn' WITH NORECOVERY
Go
```
We now have the new server and database ready for mirroring. In order for us to bring this new mirror up and running, we will need to stop and remove mirroring from our initial setup. I highly recommend this step to be done in production while the lowest amount of transactions is being run on the principle. Once the mirror is stopped, there is a brief point in time that the landscape is exposed to a disaster that will allow the loss of uncommitted transactions. 

To stop and remove mirroring run the following ALTER statement from the principle database server. 

```sql
ALTER DATABASE NEEDTOMOVE SET PARTNER OFF
```

Setting the partner (mirror) off will remove all mirroring configurations saved previously as well as shut the mirror down. However, this will not remove endpoints created by configuring mirroring. In order to retain a secure landscape on your production mirror, it is a good idea to remove the endpoint at this time if no other mirrors are running.
  
To remove the endpoint if no other mirrors are running we can run the following statement

```sql
DROP ENDPOINT Mirroring
```

At this stage we are ready to configure mirroring from the principle to our new database server. While we have the mirroring endpoint already created on our principle database server, we still need to create a new endpoint on the new server. Using the script from part 1 for configuring the initial mirror, we can remove the first CREATE ENDPOINT statement and then re-sequence the steps to accomplish setting up the new mirror.

```sql
--On the secondary (mirror) run
--1
CREATE ENDPOINT [Mirroring_Migration] 
    AUTHORIZATION [PMHCtkrueger]
    STATE=STARTED
    AS TCP (LISTENER_PORT = 5024, LISTENER_IP = ALL)
    FOR DATA_MIRRORING (ROLE = PARTNER, AUTHENTICATION = WINDOWS NEGOTIATE
, ENCRYPTION = REQUIRED ALGORITHM RC4)
--2
ALTER DATABASE [NEEDTOMOVE] SET PARTNER= N'TCP://PrincipleServerName:5022'
 
--On the principle run
--3
ALTER DATABASE [NEEDTOMOVE] SET PARTNER= N'TCP://MirrorServerName:5024'
--4
ALTER DATABASE [NEEDTOMOVE] SET SAFETY OFF
 
--on both instances
--5 + 6
EXEC sys.sp_dbmmonitoraddmonitoring
```
You may notice that my endpoint in the script has a change to the name from Mirroring to Mirroring_Migration. This is due to my local installations and the configuration not accepting the same name of Mirroring while bringing mirroring up on the new instance. I'm still investigating this problem in my test lab but would very much like to hear from others if they have had this scenario in their own labs with multiple instances side-by-side.

We now have mirroring going from our old production server to our new production server successfully. During that process there was a brief time where the failover process was vulnerable but as you can see the time lines are short and acceptable in migration steps in most scenarios. 

## _Wrapping it up..._

There is only one step that remains in our migration of the database to our new server. We need to fail the mirror over and typically this requires the applications that are using the data source to either be restarted or require a time with no activity on the databases. More often than not, the applications you have on site are all going to have different configurations and needs when a data source is altered. Some will be affected by IP changes, server name changes, services changing etc... All of the things identified in the applications that need to be configured should be weighed heavily before actually doing the failover to the new server. Once all of these are carefully planned out the actually failover is quick and painless from the database side. 

The failover can be accomplished by means of SSMS or a statement executed on the principle database server. 

From within SSMS you can right click the database and select properties. Then from within the Mirroring page there is a failover button available to cause the failover event.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//ssms_failover.gif" alt="" title="" width="454" height="408" />
</div>

To accomplish this same task from TSQL we can execute the following statement

```sql
ALTER DATABASE NEEDTOMOVE SET SAFETY FULL
GO
ALTER DATABASE NEEDTOMOVE SET PARTNER FAILOVER
GO
```
Then we can go on the new principle and reset out safety level to off with

```sql
ALTER DATABASE NEEDTOMOVE SET SAFETY OFF
GO
```
This is followed by any configuration you may need to reset applications for the users to point to our new database server.

Once all of this is accomplished you do have a choice or retaining the old hardware as the mirror but configurations should be altered to act as a primary mirror. If you retire the old server completely, setting up the new mirror is the same as we've done in this test case.

 [1]: /index.php/DataMgmt/DBAdmin/move-databases-to-new-server-with-little-1