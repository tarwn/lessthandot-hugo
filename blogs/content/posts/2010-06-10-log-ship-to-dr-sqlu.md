---
title: Log Shipping for cheap DR
author: Ted Krueger (onpnt)
type: post
date: 2010-06-10T10:50:34+00:00
excerpt: 'Welcome to day three of HA and DR week of SQL University.  Today we are going to look at cheap DR.  Yes, setting up DR can be inexpensive.  The best part of this strategy is it comes along with most of the editions of SQL Server.  The method is Log Shipping.  Log shipping (LS) has a bad name in the Disaster / Recovery (DR) world.  There are concerns with the ability to fail back to primary sites in the case of disasters, and LS is often thought of as a maintenance intense setup along with file mess.  Today’s class will go over some methods to handle these and other concerns, along with the simplicity of configuring and monitoring LS in SQL Server 2008 (R2).'
url: /index.php/datamgmt/dbprogramming/log-ship-to-dr-sqlu/
views:
  - 20248
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - backup
  - disaster recovery
  - dr
  - ha
  - high availability
  - log shipping
  - mirroring
  - restore
  - sql server 2008

---
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqlu_logo.gif" alt="" title="" width="150" height="166" align="left" />
</div>

Welcome to day three of HA and DR week of SQL University. Today we are going to look at cheap DR. Yes, setting up DR can be inexpensive. The best part of this strategy is it comes along with most of the editions of SQL Server. The method is Log Shipping. 

Log shipping (LS) has a bad name in the Disaster / Recovery (DR) world. There are concerns with the ability to fail back to primary sites in the case of disasters, and LS is often thought of as a maintenance intense setup along with file mess. Today’s class will go over some methods to handle these and other concerns, along with the simplicity of configuring and monitoring LS in SQL Server 2008 (R2). 



## **What is Log Shipping**

Log Shipping consists of three events. Backup transaction log, Copy remotely and Restore to subscriber(s). Any one primary (publisher or the logs) can have one or more secondary databases (subscriber to the logs). 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/logship_7.gif" alt="" title="" width="500" height="444" />
</div>

When configuring Log Shipping, all of the configuration settings are held in the MSDB database. 

These tables consist off the following

> log\_shipping\_primary_databases
  
> log\_shipping\_primary_secondaries
  
> log\_shipping\_monitor_primary
  
> log\_shipping\_monitor\_history\_detail
  
> log\_shipping\_monitor\_error\_detail
  
> log\_shipping\_secondary
  
> log\_shipping\_secondary_databases
  
> log\_shipping\_monitor_secondary

The system procedures for configuring log shipping are located in the master database.

> sp\_add\_log\_shipping\_monitor_jobs
  
> sp\_add\_log\_shipping\_primary
  
> sp\_add\_log\_shipping\_secondary
  
> sp\_create\_log\_shipping\_monitor_account
  
> sp\_delete\_log\_shipping\_monitor_info
  
> sp\_delete\_log\_shipping\_monitor_jobs
  
> sp\_delete\_log\_shipping\_primary
  
> sp\_delete\_log\_shipping\_secondary
  
> sp\_get\_log\_shipping\_monitor_info
  
> sp\_log\_shipping\_get\_date\_from\_file
  
> sp\_log\_shipping\_in\_sync
  
> sp\_log\_shipping\_monitor\_backup
  
> sp\_log\_shipping\_monitor\_restore
  
> sp\_remove\_log\_shipping\_monitor_account
  
> sp\_update\_log\_shipping\_monitor_info

When Log Shipping is enabled and configured, all subscribers must be in either Recovering or Read-Only (Standby) status. Placing a subscriber into a Read-Only mode is common, but when a log is applied to the subscriber, all connections must be closed. This is due to the subscriber database being required to go into recovering so the log can be applied. Often, subscribers are used for reporting so considering the state of connectivity to the database is critical in being effective for availability.

Log shipping has some definite advantages on its side for being used in DR. The overhead of the processing (backup, copy and restore) can be managed and, if thought through well, can leave a very small footprint on the normal operations of the database servers. Maintenance is very minimal for both setup and administration. Most database administrators already work closely with backup and restore, so log shipping is easy to learn. The major cost in log shipping is disk for storage, a secondary SQL Server and the network backbone to handle the files moving across the lines. This, compared to some DR cost overhead, is very minimal. 

In the following steps we will set up log shipping completely on a local default SQL Server instance. 

> <span class="MT_red">Note: as always, database size is a factor. Databases in the Terybyte range can easily be logged shipped given the resources behind it. Steps that are outlined will change greatly in time of execution due to the size of a database. An initial restore for one will take some time at first.</span> 

## **Setting up Log Shipping with SSMS**

A few things should be prepared prior to configuring Log Shipping. 

  * Share location for the log backups on the publisher
  * Share location for the log backups to be copied to on the subscribers
  * Security setup for accessing these shares (Agent account by default)

Log Shipping can be setup completely using T-SQL, but in the push for the “point, click and run” theory of SSMS, we will use it to setup, configure and get our lab running.

Log shipping is available in every edition of SQL Server but Express. In order to show our setup we will be using Developer Edition. Developer Edition is available for a very low cost and is identical in features to Enterprise. We will be using the AdventureWorks database to setup Log Shipping. If you do not have this database, you can download it here. 

  1. Open SSMS and connect to your SQL Server.
  2. Expand the databases tree, right click AdventureWorks and select properties
  3. Select Transaction Log Shipping on the right
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/logship_1.gif" alt="" title="" width="628" height="561" />
</div>

By default, Log Shipping is disabled on each database. In order to go further, we need to check “Enable this as a primary database in a log shipping configuration”. This will set the transaction log backups available for us to open and configure.

> <span class="MT_red">Note: If you have other transaction log backups running, they should be turned off prior to starting the new Log Shipping plans.</span>

  4. Click the &#8220;Backup Settings&#8221; button to open the configuration wizard.
Earlier we mentioned preparing for Log Shipping and the shares required. You can use admin shares (e.g. \onpnt_xpsd$) but this isn’t recommended as the admin shares should be for administrative purposes only. For our setup we will be using the following for processing backups:

\onpnt\_xpspub\_logs
  
\onpnt\_xpssub\_logs

  5. Enter your share into the “Network path to backup folder” field
For now, we will leave the default 72 hours to retain log backups. 

> <span class="MT_red">Note: the retention of the log backups must be taken into consideration for recovery from backups. Take into consideration the retention of other Full and Differential backups when setting this removal option. You do not want to delete log backups that could be required to recover to a point in time by restores.</span>

The log shipping configuration will add a SQL Server Agent job that will monitor the thresholds set when configuring them. If the table log\_shipping\_monitor_primary shows a backup date greater than the backup thresh hold value, an error will be raised in SQL Server. In order to actively be notified, operators need to be setup on the agent and the job so you will receive these errors as they occur. 

I would not recommend leaving the default Job name. Make use of this name so you can easily find the job and know what it is for. If you work on SQL Servers that have hundreds of jobs or Job Servers, using meaningful names in your environment make maintenance much faster and easier on everyone.

The next step is to determine the interval of the log backups. The default 15 minutes is common but in a high-transaction database, 15 minutes can mean severe loss of data in a disaster. I recommend really putting some thought into this setting. Ensure you do not hurt performance with backups tripping over themselves or occurring so often they cause problems. At the same time, ensure that you are protecting against the least amount of loss the business will accept.

Leave the default 15 minutes for now. If you want to alter this schedule, click the Schedule button and the SQL Agent scheduling window will come up. 

To show compression, next to &#8220;Set backup compression&#8221;, select Compress Backup. If you are on an earlier version than 2008 R2 and any edition other than Enterprise or Developer, this option will not be available. SQL Server 2008 R2 allows compression in Standard and Enterprise (including Developer). Pre-R2, only Enterprise and Developer have this option. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/logship_2.gif" alt="" title="" width="628" height="646" />
</div>

  6. Click OK after ensuring everything is completed as shown above.
The next step in the process is to set any subscribers and monitoring servers if you use them outside the publisher. In preparation you can restore a full backup and bring the tail log into the AdventureWorks subscriber database. This can also be done from the subscriber steps.

  7. Click Add under the Secondary databases
  8. Click Connect and connect to the instance you want to Log Ship to
  9. On the Initialize Secondary Database tab, select &#8220;Yes, generate a full backup of the primary…&#8221;
This will back the AdventureWorks database up and restore it as the database we specify to be the subscriber. Ensure if you do this lab on a single SQL Server to change the name of the Secondary database to something other than the default of the primary. 

 10. Click the restore options and ensure the data and log files go into the correct directories per your disk configurations. 
 11. Select the Copy Files tab and enter the share we created earlier (\onpnt\_xpssub\_logs)
We can leave the default schedule to restore again, but I still recommend changing the job name to a something more meaningful and easy to read 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/logship_3.gif" alt="" title="" width="628" height="543" />
</div>

 12. Click the Restore Transaction Log tab and select Standby Mode and Disconnect user in the database when restoring backups. This will be required to prevent restore problems.
 13. Click OK and OK again to save all of our configurations. 
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/logship_4.gif" alt="" title="" width="628" height="551" />
</div>

After clicking OK, a dialog will be shown while the backup and restore of AdventureWorks runs. The SQL Agent jobs that will control log shipping will also be created after these steps succeed. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/logship_5.gif" alt="" title="" width="628" height="334" />
</div>

Once the restore is done and logs have shipped, you will start to notice them moving in the publication and subscriber shares

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/logship_6.gif" alt="" title="" width="628" height="282" />
</div>

Now that LS is running we can look into the process and logging of the events. The log\_shipping\_monitor_history table is extremely useful for validating the entire process between the instances. The Message column has logged information that will explain in detail the process that is occurring:

<pre>select [message] from log_shipping_monitor_history_detail</pre>

Results:

> Starting transaction log copy. Secondary ID: &#8217;07af9839-f962-40db-9392-2107e9cc2053&#8242;
  
> Retrieving copy settings. Secondary ID: &#8217;07af9839-f962-40db-9392-2107e9cc2053&#8242;
  
> Retrieved copy settings. Primary Server: &#8216;ONPNT\_XPS&#8217;, Primary Database: &#8216;AdventureWorks&#8217;, Backup Source Directory: &#8216;\onpnt\_xpspub\_logs&#8217;, Backup Destination Directory: &#8216;\onpnt\_xpssub_logs&#8217;, Last Copied File: &#8216;<none>&#8216;
  
> Copying log backup files. Primary Server: &#8216;ONPNT\_XPS&#8217;, Primary Database: &#8216;AdventureWorks&#8217;, Backup Source Directory: &#8216;\onpnt\_xpspub\_logs&#8217;, Backup Destination Directory: &#8216;\onpnt\_xpssub_logs&#8217;
  
> Checking to see if any previously copied log backup files that are required by the restore operation are missing. Secondary ID: &#8217;07af9839-f962-40db-9392-2107e9cc2053&#8242;
  
> The copy operation was successful. Secondary ID: &#8217;07af9839-f962-40db-9392-2107e9cc2053&#8242;, Number of log backup files copied: 0
  
> Starting transaction log copy. Secondary ID: &#8217;07af9839-f962-40db-9392-2107e9cc2053&#8242;</none>

Another extremely practical usage of these tables is, in the event of a disaster, being able to later analyze data that may have been lost in log backups that did not get copied to secondary servers. The log\_shipping\_secondary has a column, &#8220;last\_copied\_file&#8221;. This column has helped me determine exactly where a subscribing database is at in the restores several times in the past. 

SSMS and built in reporting already available also provides us with a great way to monitor conditions of log shipping. Right click the database server in SSMS, scroll to reports and in standard reports, click the Transaction Log Shipping Status.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/logship_9.gif" alt="" title="" width="500" height="406" />
</div>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/logship_10.gif" alt="" title="" width="1007" height="270" />
</div>

## **Catches but not pitfalls**

Now that log shipping is setup we should discuss some pitfalls to watch for.

One big problem that comes up with any fully logged database is maintenance tasks. Index maintenance is a prime example. The growth in logging on the transaction log that happens from index maintenance while in Full recovery is large. Once this maintenance and logging begins, the log grows and this means the backups grow as they do. Coming up with the best time to do these types of maintenance tasks and the interval in which you should log ship the transaction logs is critical for this. If you have a 10GB log file and you rebuild a 5GB Index, it will take the log space to do the rebuild. This could cause growth in the log which is something we don’t really want happening a lot. So if the scheduled log backup is set to keep the free space down in the log during these tasks and normal operations, you can manage the logs very well and keep them in check while performing to the best they can.

Networks will need to be able to handle the files moving. Imagine if you start to copy a 10GB file over the regular LAN that users are connected to and working on. This happens in regular offices often. Users copy large files down or up to user home directories and it slows the entire network. The only way to clean it up is to stop the copy or wait for it to finish. This can be the same problem if the network isn’t configured to handle it. Meet with the network administrators and make sure everyone knows the traffic that will be added to the network. 

With any database that is shipped, mirrored or restored to another site, remember that logins, SQL Agent Jobs, configurations outside of the databases and objects such as endpoints or linked servers will not be sent. These must be done outside of the normal tasks. SSIS can assist in this with the use of SMO or the Transfer Server Objects Task. PowerShell can also bring in a useful container to work these tasks on schedules. It is a good idea to move these as SQL script files to the offsite server and apply them. Test and test this often.

## **Bell rang!**

Log shipping is a quick and great method for small to mid-size databases or databases that have a good foundation and planned strategy for dealing with the pitfalls of large logs. Administration is light and monitoring is very well done and built into SQL Server for use. There are added benefits of LSN tracking in the logs and file monitoring as well. The log shipping system tables have a wealth of information in them that can be used for other tasks. 

When planning your own DR strategy, leave log shipping on the list of options. The cost factor and resource utilization may have it in the lead for being a good choice for your safety measures with Disaster / Recovery.