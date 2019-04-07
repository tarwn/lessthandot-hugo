---
title: Welcome to HA and DR week!
author: Ted Krueger (onpnt)
type: post
date: 2010-06-07T08:31:04+00:00
ID: 807
excerpt: 'Welcome to SQL University HA and DR week! My name is Ted Krueger and I will be covering various High Availability and Disaster Recovery (HA and DR) strategies and methods.During the week, we will talk about defining HA and DR and introduce some points to consider while putting them into practice in your centers. Next, we will setup a database mirror and discuss the basics of this HA feature, which is available in SQL Server 2005, 2008 and 2008 R2. Finally, we will go over DR. The DR class will be composed of a Log Shipping setup and also a backup/restore setup. The HA and DR will also cover testing your solutions. That is critical to these life saving strategies - we need to know they will work in the time of need!'
url: /index.php/datamgmt/datadesign/ha-and-dr-sqlu-week/
views:
  - 37310
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqlu.gif" alt="" title="" width="213" height="230" align="left" />
</div>

Welcome to [SQL University][1] HA and DR week! My name is Ted Krueger and I will be covering various High Availability and Disaster Recovery (HA and DR) strategies and methods. 

During the week, we will talk about defining HA and DR and introduce some points to consider while putting them into practice in your centers. Next, we will go over DR. The DR class will be composed of a Log Shipping setup and also a backup/restore discussion. Finally, we will setup a database mirror and discuss the basics of this HA feature, which is available in SQL Server 2005, 2008 and 2008 R2. The HA and DR will also cover testing your solutions. That is critical to these life saving strategies &#8211; we need to know they will work in the time of need! 



### Defining the disaster and the recovery

For the SQL Server world, a disaster can mean several things. It can include loss of disk, network, physical or virtual servers, software, even a memory module and in some cases, people. Loss of any of these can be detrimental to providing data services. Anyone in charge of maintaining service levels is included in the group in charge of protecting it. Yes, don’t turn the other way looking for the person behind you. We know who you are.

It is inevitable that a disaster will hit an installation at some point in time. When a disaster does bring data services down, being prepared is the only way for you to recover with as little loss as possible. Once you are prepared for a disaster, the disaster and recovery plan should be tested to the point that it can be followed through without question. These types of drills will uncover bottlenecks in the process of recovery. These bottlenecks can cause the same type of data loss as the disaster itself, and in some cases, can make reverting back from the disaster, a disaster. The business is also important to your disaster and recovery plans. Will it need to alter its operations? Will remote abilities and processes need to be put in place? Remote call centers? Do remote “cloud” solutions need to be considered? Each business entity must be aware of the recovery plan and follow the same guidelines as the data team in bringing the remote recovery site online.

Before we start looking at the native SQL Server features for both HA and DR, let’s define the two and also discuss our options and common practices. 

## Disaster / Recovery

**Disaster:** _a sudden calamitous event bringing great damage, loss, or destruction_

<p align="center">
  <div class="image_block">
    <img src="/wp-content/uploads/blogs/DataMgmt/disaster.gif" alt="" title="" width="242" height="182" /><br /> <i>Or in other words, that mess!</i>
  </div>
</p>

**Recovery:** _the act, process, or an instance of recovering_

<p align="center">
  <div class="image_block">
    <img src="/wp-content/uploads/blogs/DataMgmt/recovery.gif" alt="" title="" width="231" height="180" /><br /> <i>On the mend and taking the pain pills</i>
  </div>
</p>

Both HA and DR can be setup for varying downtime allowances that are defined by the business needs, along with allowable time to bring the recovery systems online. DR is a strategy for a complete, offsite, failover safety control. This is used to protect against disasters such as weather, power, down circuits, fire and so on. In the event DR is called into action, a defining set of rules must be put in place that will be played out by the team in order to bring the data services and business entities back online in the allowable time span. 

Some of the variables that need to be evaluated when considering a DR strategy are:

  1. Size of databases
  2. Network capabilities
  3. Budget
  4. Features available per edition of SQL Server
  5. Maintenance load
  6. Allowable downtime
  7. Personnel resources required
  8. Initial setup downtime
  9. Will DR fit into the HA strategy?
 10. Documentation – knowledge transfer
 11. Can we test this?

These are only a few questions that can range into any business and any data team. Point #9 is critical and often overlooked. Will my DR strategy inadvertently remove my HA abilities or cause performance problems that push to removal? Database mirroring is often thought of first in a DR strategy but given the restrictions of native SQL Server mirroring and the limitations of, one mirror, you often will set this up and realize your HA setup just became limited to the features available. 

Having considered these points, we can evaluate some common DR methods:

  1. Direct SQL Server Backup and ship offsite for restore
  2. Backup and restore online
  3. Log shipping
  4. Replication
  5. Database mirroring in certain operating modes
  6. Stretch clustering or geo-clustering
  7. Third party solutions including disk and SAN replication

The primary objective with a DR strategy is to lose as little data as possible in the event of a complete disaster at the primary sites, and make that data readily available to the business, with as little interruption as feasible. In short, a total loss and complete a recovery.

Of all of these options, Log Shipping is probably the most common in small to mid-size companies and a lower administrative burden on the physical resources available (including people and hardware). It is also one of the least expensive options for a DR strategy. Log Shipping takes scheduled transaction log backups on a primary database and ships them offsite where they are restored to another, synchronized database. 

HA is a common reason to check database mirroring off the list as an option for DR. But Database Mirroring is virtually real-time in means of a recovery strategy, is it not? Ah, but we have HA to handle this for us. The two form a great partnership when working in harmony.
  
Geo/Stretch clustering is not very common due to the network and systems load. Essentially this strategy consists of SAN replication and mirrored data centers. The networks requirements for this clustering method are very large. In the closing of this article a link will be found to a recently released paper written by, Paul Randal. Paul took the time to put some real-world setups for large and some mid-size businesses on paper. The paper is as expected from Paul, excellent and something to read. 

Third party solutions can be extremely costly but, given the needs, worth the added cost. A great example of a third party strategy is utilizing Quest Litespeed for compressing and setting up log shipping. Litespeed has high compression abilities with log shipping and given an easy console for managing and editing plans. Given a scenario such as a 20GB transaction log backup, compression can bring that down 90% making the file 2GB instead of 20GB, with much less impact on the network. With SQL Server 2008 Enterprise and SQL Server 2008 R2 we do have compression available which is another feature to weigh. 

## Automation and DR

DR by itself is not commonly an automated failover process. In the event of a disaster, a decision must be made to go to DR. This decision requires weighing the cost and losses heavily. In the case of methods like Log Shipping, reversing the log shipping process once the primary site is recovered is a great option. However, Log Shipping is often blamed as a bad choice due to the difficulties in reverting back once the primary site is recovered. With some ingenuity, Log Shipping and other failback methods can be used effectively. This will prevent long downtimes when reverting back after a disaster. 

## High Availability

**Availability:** _The quality or state of being available_

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/dis_rec.gif" alt="" title="" width="359" height="201" align="center" /><br /> <i>If your database server ever has something in common with this gif, we should talk</i>
</div>

HA is defined as a true method of having the data available 100% (give or take some seconds) of the time with minimal to no loss of data availability in the event of a failure. In most cases, this is protection against hardware and human failures locally. Windows Clustering is a true form of HA; Database Mirroring given the appropriately configured running mode is also a true form of HA. Replication and log shipping are on the border of being considered HA, but are often used in situations where loss is acceptable and the amount of downtime is over seconds without affecting business operations. Log Shipping can have adverse effects on the performance of a database server if the schedule between the log backups is too small. Replication can be, and is, used in place of Database Mirroring, but cannot be used all of the time with some database designs, and is also more prone to a Safety Off scenario which equates to data loss vulnerabilities. Replication is also a much more administrative-intense strategy. Replication should not be ruled out due to some of these things but they must be part of the decision of your strategy. 

With the major changes to SQL Server in release 2005, Database Mirroring became a long awaited option. Prior to Database Mirroring, creating a true automated failover response and safety on strategy was difficult and costly. With Database Mirroring, available in both Standard and Enterprise Editions, came a feature that adds a great deal of security for SQL Server while retaining a cost effective implementation. Database Mirroring consists of 3 primary operating modes, which must be properly understood in order to maintain true High Availability. 

  * High Availability (Synchronous Mirroring with Safety On) &#8211; Includes 3 designated SQL Server instances &#8211; Principal, Mirror and Witness.
  * High Protection (Synchronous Mirroring with Safety Off) &#8211; Includes 2 designated SQL Server instances &#8211; Principal and Mirror.
  * High Performance (Asynchronous Mirroring) &#8211; Includes 2 designated SQL Server instances &#8211; Principal and Mirror.

High Availability operating mode is truly HA with the automated failover capabilities. Some critical aspects to automated failover need to be considered just as considerations come in with DR failover events. Two questions we can ask ourselves before moving forward are: Can the applications supporting the business handle an automated failover? Can the application’s performance handle this operating mode?
  
The second question is one that must be answered by testing and knowing the load on the applications with the equation of the infrastructure and server resources. Mirroring over a WAN, for example, will weigh heavily on the ability to retain the synchronous operations of writing to the logs. In many cases, switching to asynchronous and giving up the automated abilities built in is required. In these cases, monitoring the mirroring state is required so you can make decisions on failing over to the mirror. In-place scripts can then be executed, if the strategy has been set and tested for stability. 

### Whoa, that was a lot to eat 

We have gone over quite a bit in a short time and touched on only one or two features that allow us to obtain HA and DR natively in the feature set of SQL Server 2005, 2008 and 2008 R2. The objective of this class was to show the options that are available in an “out of the box” configuration of SQL Server with a lowered cost ratio. The true analysis process of determining the resources and features that the business requires to maintain both HA and DR will be driven by the requirements of the data availability and acceptable loss. It is key to not only document the process of all events required in each type of disaster but even more crucial to test these events in the live systems. Yes, pull the plug and test your HA and DR on a set schedule to test it works. 

#### Further investigation and planning in HA/DR

References/Resources to further your knowledge on strategies in HA and DR in real life case studies:
  
[Beginning stages of a DR plan for SQL Server][2]
  
[Proven SQL Server Architectures for High Availability and Disaster Recovery][3]

If you liked this SQL University post, please take a moment to fill out the [SQL University Course Evaluation][4] and select HA/DR Week. Thank you!
  
[SQL University and why you should be attending][5]

 [1]: http://sqlchicken.com/sql-university/
 [2]: /index.php/DataMgmt/DBAdmin/beginning-stages-of-a-dr-plan-for-sql-se
 [3]: http://blogs.msdn.com/b/tommills/archive/2010/06/02/new-sql-server-2008-r2-high-availability-whitepaper-published.aspx
 [4]: https://spreadsheets.google.com/a/sqlchicken.com/viewform?hl=en&formkey=dDBoSW02QldrTTc2dER3WVZheUlEX3c6MQ#gid=0
 [5]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-university-and-why-you-should-be-att