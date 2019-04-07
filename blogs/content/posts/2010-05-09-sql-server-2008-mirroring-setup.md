---
title: Mirroring Hands On with Developer Edition
author: Ted Krueger (onpnt)
type: post
date: 2010-05-09T22:33:29+00:00
excerpt: 'Time to get Dirty!  Today we are going to get down into actually configuring a basic mirror using Developer Edition.  Developer Edition is a great tool that is extremely inexpensive.'
url: /index.php/datamgmt/datadesign/sql-server-2008-mirroring-setup/
views:
  - 7707
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - high availability
  - mirroring
  - sql server 2008

---
## Time to get our hands dirty

We&#8217;ve gone over [Planning your hardware for SQL Server Mirroring][1] and [Planning your SQL Server mirroring landscape][2]. Today we are going to get down into actually configuring a basic mirror using Developer Edition. Developer Edition is a great tool that is extremely inexpensive. At the time of this writing, [the cost was still only $47][3].

To set our mirror up we will need two instances. The instances for our examples will be located on the same physical hardware. 

> Note: Catch number one in this lab setup; when configuring mirroring in which the mirror, principal and/or witness are on the same physical machine, ensure that your endpoint ports are configured so each entry point is aware of its own path. Example: You principal, mirror or witness cannot share the same port of 5022. 

When configuring mirroring, replication or log shipping on one local machine; be sure to document your configurations for each HA or DR test. Documentation can save you from false errors and testing not functioning as it should. Not doing this often will cause you headaches trying to debug configurations when in reality; the configuration problem is simply a problem with the instances colliding.

So for example with this mirroring lab we are configuring, you may want to list something like the following

  * Instance DEV2008_MIRRORLAB
  * Using Port 7088 for Mirroing ENDPOINT
  * Service Credentials set for security (account name)
  * Mirroring set to High Performance mode
  * Database in setup is AdventureWorks

When setting up mirroring there are two points to troubleshoot if the mirror will not start synchronizing. Those are:

  1. Restore of the tail log wasn’t done or successful – restore procedure as a whole
  2. Firewall preventing the endpoints from talking – test telnet to your ports

Below is the setup used if you want to follow along exactly while configuring your own lab.

  * Primary: SQL Server 2008 Developer Edition. Named Instance: MACHINE2008DEV
  * Secondary: SQL Server 2008 Developer Edition. Named Instance: MACHINE2008DEVMIRROR
  * Secondary: SQL Server 2008 Express Edition. Named Instance: MACHINESQLEXPRESS

The Express version of this setup will act as the witness for certain configurations we will work together on. The database we will configure in these examples will be AdventureWorks. This database is available for free download for SQL Server 2008 on [Codeplex][4]

We are going to jump right into setting the mirror up. Our first configuration will step through showing how to set synchronous mirroring up with safety off. This is also known as High Protection. The reason for this configuration is to have confidence in your HA solution will commit all the data changes to the mirror before allowing a return command to the calling source. 

## The preparation

In order to ensure the databases we want to mirror are ready for mirroring itself, we need to check a few things first. Full recovery is a requirement of mirroring. This is required for logging purposes. To check that the AdventureWorks database is in Full Recovery, we can run the following

<pre>IF (DATABASEPROPERTYEX('AdventureWorks', 'RECOVERY') <> 'FULL')
 BEGIN
  ALTER DATABASE AdventureWorks SET RECOVERY FULL
 END;
 
--SELECT DATABASEPROPERTYEX('AdventureWorks', 'RECOVERY')</pre>

If you have an existing AdventureWorks database on the SQL Server you will be using for the mirror, you will need to know the mdf, ldf and any ndf’s and their locations. 

You can check for these files using sysaltfiles

<pre>SELECT * FROM master.dbo.sysaltfiles</pre>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_0.gif" alt="" title="" width="478" height="89" />
</div></p> 

Backing up the principal to get the mirror ready is the next major piece to preparation. Without the full backup and any logs or differentials in between the tail end log backup, we will not be able to get the mirror into a synchronized state in which the process can successfully match where the logging is. 

To create the Full and Tail end log backups, execute the following 

<pre>BACKUP DATABASE AdventureWorks TO DISK = 'C:AdventureWorks_full_initial.bak'
GO
BACKUP LOG AdventureWorks TO DISK = 'C:AdventureWorks_taillog_initial.trn'
GO</pre>

We can now restore the database to the mirror SQL Server. In the case of our AdventureWorks database, we have two file groups as well. These files groups pose no complication to the mirroring landscape other than they need to exist on the mirror as well as the principal. The only place we need to reference them is in the restore of the database as well.

<pre>RESTORE DATABASE AdventureWorks 
FROM DISK = 'C:AdventureWorks_full_initial.bak'
WITH NORECOVERY,
MOVE 'AdventureWorks_Data' TO N'C:sql2008NEEDTOMOVE_mirror.mdf',
MOVE 'AdventureWorks_log' TO N'C:sql2008NEEDTOMOVE_mirror_log.ldf',
MOVE 'YearF1' TO 'C:sql2008AdventureWork_YearF1.ndf',
MOVE 'YearF2' TO 'C:sql2008AdventureWork_YearF2.ndf'
,REPLACE,NORECOVERY
GO
RESTORE LOG AdventureWorks FROM DISK = 'C:AdventureWorks_taillog_initial.trn' WITH NORECOVERY
GO</pre>

## Jump right into configuring the mirror

# One way (SSMS)

Right click the database from Object Explorer and select Properties.

In the Database Properties dialog, select Mirroring. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_1.gif" alt="" title="" width="525" height="469" />
</div>

To set mirroring up we must go into the “Configure Security” wizard. This is slightly off on what it means. In this wizard we will in all, setup security, endpoints and the location of the instances.

The first tab will ask if we want a witness or not. A witness can be located on the mirror if needed but given the \*free\* status of SQL Express, it is a good choice to use as a witness. This is best located off both the principal and mirror instances. 

We will select No here but by default it is set to Yes.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_2.gif" alt="" title="" width="546" height="491" />
</div>

On the next screen we jump right into the principal instance selection. The settings will be pre-filled out given the instance you started the wizard from. The only change we will make here is the port to 7088

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_3.gif" alt="" title="" width="546" height="491" />
</div>

At the next screen we will need to connect to the server that will act as our mirror. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_4.gif" alt="" title="" width="517" height="479" />
</div>

Once the instance, change the port number to 7089.

Security follows for both instances. Enter the service accounts for your own environment and click, Next.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_5.gif" alt="" title="" width="535" height="481" />
</div>

Once security is configured, the mirroring setup is complete. Clicking Finish will complete the configuration. This will also create the endpoints on the instances in order to start mirroring. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_6.gif" alt="" title="" width="535" height="481" />
</div>



<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_7.gif" alt="" title="" width="376" height="153" />
</div>

Once you exit the wizard, a dialog will come up asking if you want to start the mirror. At this point we want to do the start up process so click Start.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_8.gif" alt="" title="" width="456" height="299" />
</div>

Check it out, it works!

<pre>select * from sys.database_mirroring where mirroring_guid is not null</pre>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/setupmirror_9.gif" alt="" title="" width="628" height="127" />
</div>

In the real world, the mirroring\_state\_desc will say &#8220;Synchronizing&#8221; much longer than the amount of time it takes to execute this query. The initial startup of the mirroring requires it to catch up, and with larger databases that are active, the synchronizing status can last minutes.
  
We now have a mirroring running in High Protection. 

As with 99% of all SQL Server configurations, we can do this all with TSQL as well. In some cases DBAs prefer this as it may expose options that are harder to get to from the wizards. 

## The other way? T-SQL

To do this in TSQL the steps are much shorter. Following the script below we can execute a series of statement to set the ENDPOINTs up, backup and restore the databases along with start the mirror by directing the partnership to each other.
  
Follow the steps commented to ensure each is executed on the correct instance. 

First, to clean up the previous mirroring session we setup from SSMS, remove mirroring by executing this statement from the principal. 

<pre>ALTER DATABASE AdventureWorks SET PARTNER OFF;</pre>

Then drop the endpoints on both the principal and mirror by using 

<pre>DROP ENDPOINT [Mirroring]</pre>

Use the BACKUP and RESTORE scripts and steps as discussed in the beginning of this post to prepare the databases for mirroring. 

Then run the following on the instances listed in comments and the order noted

<pre>--On the principle run
--1
CREATE ENDPOINT [Mirroring] 
    AUTHORIZATION [service_account]
    STATE=STARTED
    AS TCP (LISTENER_PORT = 7088, LISTENER_IP = ALL)
    FOR DATA_MIRRORING (ROLE = PARTNER, AUTHENTICATION = WINDOWS NEGOTIATE
, ENCRYPTION = REQUIRED ALGORITHM RC4)
--On the mirror run
--2
CREATE ENDPOINT [Mirroring] 
    AUTHORIZATION [service_account]
    STATE=STARTED
    AS TCP (LISTENER_PORT = 7089, LISTENER_IP = ALL)
    FOR DATA_MIRRORING (ROLE = PARTNER, AUTHENTICATION = WINDOWS NEGOTIATE
, ENCRYPTION = REQUIRED ALGORITHM RC4)
--3
ALTER DATABASE AdventureWorks SET PARTNER= N'TCP://fully.qualified.domain.name.com:7088'

--On the principle run
--4
ALTER DATABASE AdventureWorks SET PARTNER= N'TCP://fully.qualified.domain.name.com:7089'

--on both instances
--5 + 6
EXEC sys.sp_dbmmonitoraddmonitoring</pre>



## Moving on with mirroring

We&#8217;ve managed to setup our mirror together and show that our data is all synchronized. This is truly a huge accomplishment and you can see just how simple the base setup can be. There is flexibility in using SSMS for the entire process or moving into TSQL. For a High Availability solution, SQL Server mirroring can give you plenty of confidence in knowing the day a disaster to your server happens, your company can keep moving. 

Part 2 in the mirroring series will go over using the other two types of mirroring (High Availability and High Performance) and certificates for mirroring. </p>

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/selling-a-mirror-short
 [2]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/beef-is-in-the-mirror
 [3]: http://www.amazon.com/SQL-Server-2008-Developer-Edition/dp/B001B8EZR4
 [4]: http://msftdbprodsamples.codeplex.com/releases/view/37109