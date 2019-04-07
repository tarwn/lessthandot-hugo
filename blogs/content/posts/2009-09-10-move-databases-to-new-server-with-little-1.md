---
title: Using Mirroring to Reduce DB Migration Downtime (Part 1)
author: Ted Krueger (onpnt)
type: post
date: 2009-09-10T10:28:44+00:00
ID: 549
url: /index.php/datamgmt/dbprogramming/move-databases-to-new-server-with-little-1/
views:
  - 12334
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
## _The History_

A few years ago I needed to move an active set of databases from their current environment to a new server. The move was for a much needed hardware upgrade and, given the 24/7 nature of the business, required as little interruption to normal operations as possible. While reviewing the typical migration methods it occurred to me that we might be able to utilize mirroring to reduce the downtime at the cost of a little more up-front preparation. The idea was to create another database on the new server with all the jobs, security, and supporting objects restored. Once this was completed we could then restore the database from the outdated server on the new server and after restoring all the logs to bring the data in synch, start the new database as the mirror. When migration day came I would, theoretically, be able to kill the original server and watch the new mirror take the whole load, without any associated downtime. This not only would reduce my downtime window but also give me a great deal of freedom to schedule the failover to the new server. The failover plan went well. I had scripts to update the data source connections for the front-end applications and required only that the end users shut down and restart their applications. Failover took seconds instead of the minutes or hours involved in other options we had considered.

## _Building a Lab Version_

If you already have a mirrored setup there are some additional caveats that I will mention later in the article.

Rather than just tell you about my experiences, let’s build a lab to play with. I’m going to make some assumptions so we don’t need to start with a How-To on installing SQL Server, that’s another article. I’m assuming the following things:

  * You have an existing mirror without a witness (High Performance) 
  * SQL Server 2005×86 Enterprise 
  * No compression or third party tools are being used 
  * These are on licensed developer editions. This means mirroring is disabled by default. Set trace flag 1400 in the startup in order to use mirroring in a “DEVELOPMENT” lab.* 
      1. Option 1 is add ;-T1400 to the startup in configuration manager 
      2. Stop MSSQLSERVER and issue a, “NET START MSSQLSERVER /T1400
  * Last, we are starting with clean instances with no endpoints etc… 

In my test lab (local laptop ;-)) I have two instances I use for testing configurations, writing blogs etc. These are developer instances (identical to Enterprise). As we work through the lab setup, be aware that the Developer version of SQL Server allows us to use Enterprise features that are not available in the Standard version. If your production environment is Standard then there are several features you will not be able to rely on, such as true asynchronous

Here is the landscape you will need for this exercise

Instance 1 (The principle) MYLAB
  
Instance 2 (The mirror) MYLABMIRROR
  
Instance 3 (The new principle) MYNEWLAB

To set up your mirror initially let’s do the following

sql
CREATE DATABASE [NEEDTOMOVE] ON  PRIMARY 
( NAME = N'NEEDTOMOVE', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOMOVE.mdf' , SIZE = 2048KB , MAXSIZE = UNLIMITED, FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'NEEDTOMOVE_log', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOMOVE_log.ldf' , SIZE = 1024KB , MAXSIZE = 2048GB , FILEGROWTH = 10%)
GO
```

If you have your model set to Simple recovery you will need to convert to Full recovery before you will be able to setup the initial mirror.

Next, create your secondary database (the mirror)

sql
CREATE DATABASE [NEEDTOMOVE] ON  PRIMARY 
( NAME = N'NEEDTOMOVE', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOMOVE_mirror.mdf' , SIZE = 2048KB , MAXSIZE = UNLIMITED, FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'NEEDTOMOVE_log', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOMOVE_mirror_log.ldf' , SIZE = 1024KB , MAXSIZE = 2048GB , FILEGROWTH = 10%)
GO
```

Let’s get mirroring by backing up the log on the principle so we can bring the mirror database in synch. This is always required to start mirroring. If we don’t restore the tail end of the log, mirroring will error on starting mirroring. 

Run a full backup of the principle. Since this is a new database in our lab, a full backup is required to start the full recovery process off. Once the full backup is completed, the log backup can follow. 

sql
BACKUP DATABASE [NEEDTOMOVE] TO DISK = 'C:needtomove_full_initial.bak'
```

Now run your tail end log backup

sql
BACKUP LOG [NEEDTOMOVE] TO DISK = 'C:needtomove_taillog_initial.trn'
```

Now that we have out backups ready we can start the restore process. The key to getting the mirror database ready to configure and start mirroring, is for us to bring the mirror database in synch with the principle and in recovering status. This is required so the mirror can accept transactions from the principle. The concept of a read-only (stand by) database with log shipping is on the same lines of this process. 

sql
RESTORE DATABASE [NEEDTOMOVE] 
FROM DISK = 'C:needtomove_full_initial.bak'
WITH NORECOVERY,
MOVE 'NEEDTOMOVE' TO N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOMOVE_mirror.mdf',
MOVE 'NEEDTOMOVE_log' TO N'C:Program FilesMicrosoft SQL ServerMSSQL.1MSSQLDATANEEDTOMOVE_mirror_log.ldf'
,REPLACE,NORECOVERY
```

Now we can restore the log

sql
RESTORE LOG [NEEDTOMOVE] FROM DISK = 'C:needtomove_taillog_initial.trn' WITH NORECOVERY
```

As I mentioned earlier, your database has to be restored with NORECOVERY to start your mirror. If you mess that step up and restore with recovery, you’ll need to start eh restore steps over. With the lab we setup, this can be a quick process. However with larger databases, this can be a lengthy process. 

The next step is to get the databases on each instance mirroring so we will be at the point we can start the migration of the database.

The following sequence of steps is how we will configure mirroring. The sequence is important and can be simplified by using SSMS. You can follow the wizard in SSMS by right clicking the principle database and select properties. In the mirroring page, click the configure security. This launches the, database mirroring security wizard where security and endpoint configurations can be completed. Once the wizard is completed there will also be an alert asking to start mirroring. 

For T-SQL setup the process we can follow the sequence below.

  1. Create endpoint for mirroring on the principle 
  2. Create endpoint on mirror 
  3. Alter the database on the mirror to configure the partner as the principle 
  4. Alter the principle database to configure the partner as the mirror 
  5. Alter the safety mode 
  6. Start up mirror monitoring jobs

Here is an example of a script that I wrote to execute the series of tasks. Note the helpful numbering, always a time saver when you come back to re-use to cannibalize a script 6 months later. 

sql
--On the principle run
--1
CREATE ENDPOINT [Mirroring] 
	AUTHORIZATION [PMHCtkrueger]
	STATE=STARTED
	AS TCP (LISTENER_PORT = 5022, LISTENER_IP = ALL)
	FOR DATA_MIRRORING (ROLE = PARTNER, AUTHENTICATION = WINDOWS NEGOTIATE
, ENCRYPTION = REQUIRED ALGORITHM RC4)

--On the secondary (mirror) run
--2
CREATE ENDPOINT [Mirroring] 
	AUTHORIZATION [PMHCtkrueger]
	STATE=STARTED
	AS TCP (LISTENER_PORT = 5023, LISTENER_IP = ALL)
	FOR DATA_MIRRORING (ROLE = PARTNER, AUTHENTICATION = WINDOWS NEGOTIATE
, ENCRYPTION = REQUIRED ALGORITHM RC4)
--3
ALTER DATABASE [NEEDTOMOVE] SET PARTNER= N'TCP://PrincipleServerName:5022'

--On the principle run
--4
ALTER DATABASE [NEEDTOMOVE] SET PARTNER= N'TCP://MirrorServerName:5023'
--5
ALTER DATABASE [NEEDTOMOVE] SET SAFETY OFF

--on both instances
--6 + 7
EXEC sys.sp_dbmmonitoraddmonitoring
```

Note that in this example script my port numbers are not the same. This is only because I am building the principle and mirror on the same machine, your port number will depend on how your SQL instances are configured. If you have a test lab at your disposal with default instances on several systems, you will probably use 5022 for both ports. 

In SSMS hit refresh on both of your instances. You should now see that on principle database instance, NEEDTOMOVE is showing (Principle,Synchronized). On the mirror instance you should also see that NEEDTOMOVE is showing (Mirror,Synchronized / Restoring…).

If you have the status on both databases as shown then we are ready to go to the next level and our test lab is ready. This is a typical high performance mirror without a witness. 

So now you have a new server and much better hardware to handle your rapidly growing databases. How do you move it when they say you get minutes of downtime? Use the mirror!

## _The Migration_

Here is what your configuration should appear in the mirroring section of the principle database after all of this

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//mirror_prop.gif" alt="" title="" width="454" height="408" />
</div>

Here is what your configuration should appear in the mirroring section of the principle database after all of we’ve done so far. 

As we covered earlier in the article, you are vulnerable at some point by basically being without the mirror. In all, I accept this because you are still at one point vulnerable on other migration methods. You’re going to have to reset the mirror no matter what so why not get it done now and use it to move the database to our new server. This also helps in taking detach and attach out of the picture and the problems we’ve all seen in troubleshooting why a database will not attach. Second, you can tune the new server before go live. Actually there are a lot of reasons I like this method. The limited user community interruptions are always the best ones.

Recall we have a third instance in our lab named MYNEWLAB. This is where we will move the mirror to. Here are the basic steps to do the move

• In a production setting, running a full backup during operating hours may be difficult and affect performance. This usually leads us to restore from normal full, differential and log backups. If operating times and your current setup accept a full backup to be run at the time you start this process, then a fresh full backup is a good starting point. For our lab and tests we will start with a full backup, so let’s run a full backup of the principle along with a transaction log backup to capture the tail end of the logs.
  
• Restore the full backup to the new instance with norecovery. After successfully restored, restore the transaction log backup again with norecovery.
  
• Stop and remove all mirroring on the current landscape
  
• Configure mirroring from the principle to the new server – MYNEWLAB
  
• Schedule the time in which the least amount of connections will need to be reset for a failover of the mirror to the new instance. 

Done! 

## _Caveats_

If you have a current mirror going, you still can use this method but you have to be aware of the vulnerable time slots while you move the mirror. Of course there are performance concerns like OS paging while you’re going at it and moving files all around and disk I/O. Just keep those in mind when you start resetting mirrors and use common sense while moving your backups. Remember that high availability has a witness involved as well. This means the witness handles the failover of a mirror from the principle to the secondary automatically. You can still keep that witness alive but it affectively will be down during the setup and ready process of throwing the switch. If you are in high performance then you only have the vulnerability of actually setting your partner of the database off and flipping the new one on with the short endpoint create and partner statements.

## _To be Continued…_

OK, I realized this write up is getting far too long so I’m splitting it up into parts. This will be part 1 of course. In here we showed how the concept will work, how to setup the mirror for planning the switch over and the key issues and notes we need to account for in the process. Part 2 will be the actual migration, you can find that here: [Using Mirroring to Reduce DB Migration Downtime (Part 2)][1]

 [1]: /index.php/DataMgmt/DBAdmin/using-mirroring-to-reduce-db-migration-d-2