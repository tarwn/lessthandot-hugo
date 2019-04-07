---
title: SQL Server DBA Tip 13 – SQL Server built in DR/HA Solutions
author: Ted Krueger (onpnt)
type: post
date: 2011-05-12T11:25:00+00:00
ID: 1171
excerpt: 'SQL Server has several options available for a stable DR/HA solution at a minimal cost.  There is a grey area in which the time to start looking to products from third party vendors may allow more manageable DR/HA solutions.  This grey area typically be&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-dba-tips-sql-server-built-in-dr-ha/
views:
  - 4919
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <a href="/media/blogs/DataMgmt/-56.png?mtime=1305135956"><img src="/wp-content/uploads/blogs/DataMgmt/-56.png?mtime=1305135956" alt="" width="76" height="53" align="left" /></a>
</div>

SQL Server has several options available for a stable DR/HA solution at a minimal cost.  There is a grey area in which the time to start looking to products from third party vendors may allow more manageable DR/HA solutions.  This grey area typically becomes more prevalent when database size grows or geographical distance is added to a SQL Server landscape.  However, the features in SQL Server can scale to the higher percentages of database size that DBAs are handling. This can be seen in the [survey on SQL Skills][1] that most databases are in 100GB to 500GB range.

**What are the choices in DR?**

The business has more decision making factors in DR/HA than most like to admit.  The cost of downtime and cost of loss of data all play an important factor on what you can use for DR/HA.  Once the business needs are documented and facts are drawn, you can start planning the best setup for DR and HA.

SQL Server has more DR solutions than HA simply due to the fact that DR is a process that allows for more time allocated to recovery.  These options up to SQL Server 2008 R2 include the following.

  * Log Shipping
  * Backup and Restore
  * High Performance Mirroring
  * Peer-To-Peer Replication
  * Copy Database
  * SQL Server Integration Services Export/Import processing

_Log shipping_ is a solid method for retaining a copy of your data in an offsite location.  Setup and maintenance are much less of an overhead with this as a DR solution than a lot of the other choices.  Depending on the log growth and overall utilization, log backups can be done anywhere from minutes to hours apart to handle the copying of data to the designated recovery area.  The concerns that come with log shipping revolve around network utilization and tail-end log loss (meaning, in the event of a disaster, there is a possibility of losing the last log backup).  One benefit to log shipping is having the ability to retain the subscribing database as a reporting resource.  Log shipped databases can be set in read-only or stand-by mode.  The only problem with this solution comes with the restoring the logs, which requires users to be disconnected. 

_Backup and Restore_ is the foundation for all disaster and recovery configurations.  This option is much more than DR in the fact it should always be performed side-by-side with all other DR plans. 

_High Performance Mirroring_ (or Asynchronous Mirroring) is listed for DR to cover databases that have a DR requirement that closely resembles the needs of HA.  With HPM, transactions are not held up by the mirror which allows the originating transactions or clients to not be heavily impacted by the mirroring session.  The major problem that comes with this type of utilization of mirroring is the loss of using mirroring as a HA solution.  A database can only have one mirroring session configured.  In Denali this will change a bit and will be discussed in a later post. 

_Peer-To-Peer Replication_ (P2P) is another borderline HA solution that can be used for DR.  P2P has heavy restrictions for utilizing it on your databases and SQL Servers.  These considerations and restrictions can be seen on MSDN in detail, [Peer-To-Peer Transaction Replication][2].  P2P opens a lot of doors to geographically distant instances and DR.  This is why the use of it and feasibility in your environment should be carefully considered.

_Copy Database and SSIS_ can both be placed in the same category.  Copy Database is an option but an option that should be used when the allowable loss is great.  If a solid plan is designed with copy database used as a nightly objective to move a database to another location for recovery, it should be a DR setup that is coupled to backup and recovery.  The reasons copy database causes loss is the concerns in locking and processing time it takes to perform the task.  SSIS adds much benefit to moving data like replication and even log shipping.  Problems arise in development and skill levels required to engage SSIS as a DR strategy for moving data as well as network, locking and other concerns to the active database.  Smaller databases that have been designed for narrow tables and data types that allow for rapid data movement in and out, lend themselves to SSIS being a great solution for moving data.  This movement of data can be used for reporting and other purposes while retaining the copied data for recovery purposes.  

**What are the choices in HA?**

High Availability (HA) brings a new element to the concept of recovery.  The allowance is again dictated by the business.  This is more of a decision on the needs of implementing any HA solution however.  It is very uncommon to find a business with a data loss allowance that can be met with log shipping.  True HA requires an automated and nearly real-time fail safe mechanism allowing for clients to “always” have data availability.  Log shipping does not provide this.  Even if setup schedules for log shipping are down to 3 or 4 minutes and SSIS or some other custom solution is put in place to automatically fail an instance over; Log Shipping has a greater chance for data loss than other HA solutions, minutes of downtime to make the change and in most cases, it’s not fully available for automated failovers.  If Log Shipping is mentioned in HA planning in your environment, definitely question how effective it will be.

So why then is Log Shipping listed here, “[High Availability Solutions Overview][3]”?  Truly, it should not be in the case of a true HA definition of always having data available.  This requires an automated solution for failing over to the secondary sources in the event of a disaster.  The tip in evaluating options secondary to the topic of this post is; consider the concept of HA being an automated failover process.

What are our choices built into SQL Server?  With High Availability and SQL Server, our choices are much fewer than with DR.  The requirement of “always available” causes this.  Again, Denali has addressed a lot of these but that is not in scope since Denali is not yet available. 

High Safety Mirroring is the primary option in SQL Server.  This is also referred to as Synchronous mirroring with high safety by use of a Witness.  High Safety Mirroring in SQL Server enlists a Witness server to monitor the state of the mirroring session.  The events that cause the state of the mirrored session to change are automatically handled by the Witness.  High Safety Mirroring also implements methods that force transactions to be committed to the mirrored database before allowing the return of the transactions state to the initiated source (client or user).  This promotes an extremely low level of possible data loss of committed transactions. 

Clustering is outside SQL Server in a way and handles the hardware aspect of HA.  This is often implemented with Mirroring or other mechanisms that allow for hardware and data to be fully initiated in a HA solution.  Without clustering hardware, implementing HA for SQL Server and the databases in it, may not be sufficient enough to meet the _always available_ needs.  Clustering is not a requirement to meet those requirements in some cases.  Applications (.NET and others) have the ability in configurations to attempt failover connections themselves.  This is extremely beneficial when cost is a factor, as the cost of an additional production database server may lead to a budget crisis, because you need to purchase identical hardware for clustering sessions. 

Replication is also transactional process which can be considered for use as a HA solution.  The main problem with Replication is the restrictions it has on what can be replicated and even what databases can enlist the use of replication. 

**When and how to yell disaster?**

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-57.png?mtime=1305135956"><img src="/wp-content/uploads/blogs/DataMgmt/-57.png?mtime=1305135956" alt="" width="72" height="48" align="left" /></a>
</div>

Debating the choices to use will be impacted once determining the levels of a disaster that your unique databases can handle.  [Beginning stages of a DR plan for SQL Server][4] goes over steps of a disaster recovery planning.  Once you’ve gone through these steps and how they fit into your environment for each database, these choices become easier. 

Make these decisions with great scrutiny.  Placing a stable and tested DR and HA solution in place will ensure the data the business relies on so heavily is available to sustain revenue for the years to come. 

**Resources**

[Mirroring Hands On with Developer Edition][5]

[Log Shipping for cheap DR][6]

**[High Availability with SQL Server 2008 &#8211; by Paul S. Randal][7]**

**High Availability/ Disaster Recovery SQL University Geoff Hiten, Robert Davis and Allan Hirt******

[SQLU Post #1: What’s In a Name? (Part One)][8]

[SQLU HA/DR Week – Database Mirroring Performance Counters][9]

[SQLU Post #2: Remote Hosting, Availability, and You: (Im)Perfect Together?][10]

HA/DR Week – Ted Krueger

[Welcome to HA/DR Week][11]

[The SQL Server backup – foundation of any Disaster/Recovery][12]

[Log Shipping for Cheap DR][13]

[SQL Server and High Availability][14]

 [1]: http://www.sqlskills.com/BLOGS/PAUL/post/Bigger-is-better.aspx
 [2]: http://technet.microsoft.com/en-us/library/ms151196.aspx
 [3]: http://msdn.microsoft.com/en-us/library/ms190202.aspx
 [4]: /index.php/DataMgmt/DBAdmin/beginning-stages-of-a-dr-plan-for-sql-se
 [5]: /index.php/All/?p=839
 [6]: /index.php/All/?p=871
 [7]: http://msdn.microsoft.com/en-us/library/ee523927.aspx
 [8]: http://sqlha.com/blog/2011/04/18/sqlu-post-1-whats-in-a-name-part-one/
 [9]: http://www.sqlsoldier.com/wp/sqlserver/databasemirroringperformancecounters
 [10]: http://sqlha.com/blog/2011/04/24/sqlu-post-2-remote-hosting-availability-and-you-imperfect-together/
 [11]: /index.php/DataMgmt/?p=863
 [12]: /index.php/DataMgmt/DBAdmin/the-sql-server-backup-foundation-of-any
 [13]: /index.php/DataMgmt/DBAdmin/log-ship-to-dr-sqlu
 [14]: /index.php/DataMgmt/DBAdmin/sql-server-high-availability