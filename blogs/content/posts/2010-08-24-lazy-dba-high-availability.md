---
title: 'The Lazy DBA Series: High Availability'
author: Ted Krueger (onpnt)
type: post
date: 2010-08-24T14:11:44+00:00
excerpt: "Over the years I've had the chance to help a lot of people when it came to SQL Server. One of the things I have enjoyed the most in that time was helping with HA and DR topics. In my opinion, a truly recognized accomplishment for any DBA and Systems Team is to successfully secure the companies data and systems. The security I'm referring to isn’t to deny or grant a user access to data, but the security of data from disasters.Disasters can happen at any time. They never give us much warning either. Luckily, over many of our careers, we will never see a true disaster in action. Some will never have the opportunity to even see their High Availability go into action other than tests. Does that mean we don’t need these mechanisms and safeguards in place? (That was even hard to write) From the largest database down to the smallest script, everything should have a recovery plan. If it is important to the business and holds a key function, it has value and is worth the cost of adding the HA and DR solution to secure it.The topic at hand is High Availability, so let’s not accidentally cross to the DR side."
url: /index.php/datamgmt/datadesign/lazy-dba-high-availability/
views:
  - 6082
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database administration
  - sql server
  - sqlpass

---
Over the years I’ve had the chance to help a lot of people when it came to SQL Server. One of the things I have enjoyed the most in that time was helping with HA and DR topics. In my opinion, a truly recognized accomplishment for any DBA and Systems Team is to successfully secure the companies data and systems. The security I’m referring to isn’t to deny or grant a user access to data, but the security of data from disasters.

Disasters can happen at any time. They never give us much warning either. Luckily, over many of our careers, we will never see a true disaster in action. Some will never have the opportunity to even see their High Availability go into action other than tests. Does that mean we don’t need these mechanisms and safeguards in place? (That was even hard to write) From the largest database down to the smallest script, everything should have a recovery plan. If it is important to the business and holds a key function, it has value and is worth the cost of adding the HA and DR solution to secure it.

The topic at hand is High Availability, so let’s not accidentally cross to the DR side.

**Lazy High Availability**

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazydba2.gif" alt="" title="" width="209" height="209" align="left" />
</div>

We’re not going to discuss dark fiber, monstrous SAN replication technology or even Windows Clustering. Reality check: High Availability has work involved. There is no way around doing some work and taking the time to learn what you need to know to setup HA successfully. Planning must be done as well or the process of providing availability may fail. However, there is a lazy theme here and we will find it. It has been there since SQL Server 2005. In fact, the option has been there for the majority of SQL Server installations since SQL Server 2005 SP1. The feature is Database Mirroring. With SQL Server 2005 SP1, it was made available to Standard Edition installations as well as Enterprise.

Database Mirroring is not a basic architecture and does take some time researching and reading things like: the internals of how the logs are streamed to the mirror databases, redone, queued, performance impacts, threading considerations on maximum expectations of databases while using mirroring and overall internal error handling. Mouth full! OK, we need to take the time to read and understand the internal functions that are performed. Regardless of the technology and use we find for it, this must be done to be successful. In the most recent article, The Lazy DBA Series: SQL Trace / Profiler, I would not send anyone off opening and running the default template in profiler on a production database server while the peak of the company&#8217;s processing is being done. Reading about these tools and features is a must and can never be skipped in the process. (Ok, preaching done!)

Database Mirroring setup on the other hand, is very straight forward and the setup is painless once you have the knowledge. In fact, the most painful part in Database Mirroring will probably be the time waiting to copy exceedingly large databases to your designated mirror SQL Server over your network. Even troubleshooting errors have a small list of areas that cause setup problems. These common problems fall into mirror database not in a recovering level to start the redo process, security, forceful blocking such as port blocking and network configurations.

Everything that you need to perform a successful mirror of a database is right there in SQL Server (minus the hardware). Database Mirroring works utilizing [ENDPOINTS][1] as the primary communication method. Again, endpoints are part of SQL Server and easily configured. The best lazy part of all of what we are talking about is, this can and most commonly is, all done with the Database Mirroring Wizard. Everyone hates wizards, right? Sorry everyone, but some of those wizards make my job go fast and allow more important things to be done. You can see the complete setup using the wizard in this article, [Mirroring Hands on with Developer Edition][2]. As you probably guessed, look for a future Lazy DBA article on Wizards (and their magic spells).

Years ago, High Availability cost us a lot in time and money. In fact it was even a stressful task to propose to management the funding required to put HA solutions in place. However, recent years have seen more hardware for much less cost. Disk is no longer in the hundreds of thousands for a solid solution and server technology can be purchased with a solid internal backbone of technology at a great price.

Database Mirroring isn’t a replacement for clustering and should/can be used in conjunction. However, it can be used while clustering is not available. In situations where database servers are designated as mirrors and with newer methods in handling failover events in connections, the physical server itself is not as critical of a piece as it once was. Every application has needs and the statement I just made can be torn apart by those needs. Make sure you research the task of a failover itself before thinking things will automatically start working simply because a mirror became your principal.

**Recap the laziness**

Setup is a task that can be accomplished with much less effort that was needed in previous years of HA technology if Database Mirroring fits the strategy.

[Monitoring can be done with Event Notifications][3] which is a quick setup and solid method.

Licensing is not as much of an issue as you may think. If the Mirror is designated as a Mirror, it does not require a license until in use due to a failover.
  
From the SQL Server 2008 pricing and licensing document (this is also the case in SQL Server 2005) 

> _[Passive Servers. The passive server does not require a license given that no queries are being executed against it.][4]_ 

> Be careful with this. No queries means, no queries. Make sure you read the licensing guidelines thoroughly before making your purchase or take the time to contact Microsoft with questions.

Have fun because you really can have HA at a low cost on both price and maintenance sides.

 [1]: http://msdn.microsoft.com/en-us/library/ms179511.aspx
 [2]: /index.php/DataMgmt/DBAdmin/sql-server-2008-mirroring-setup
 [3]: /index.php/DataMgmt/DBAdmin/how-to-monitor-database-mirroring
 [4]: http://download.microsoft.com/.../2008%20SQL%20Licensing%20overview%20final.docx