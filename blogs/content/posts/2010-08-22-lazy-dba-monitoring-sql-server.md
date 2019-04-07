---
title: 'The Lazy DBA Series: Monitoring'
author: Ted Krueger (onpnt)
type: post
date: 2010-08-22T22:33:11+00:00
ID: 880
excerpt: 'There are hundreds if not thousands of T-SQL Scripts, Powershell Cmdlets, .NET programs and even old vbscript scripts and on out there to monitor SQL Server and Operating System level performance.  I’ve written hundreds of them myself over the years.  All of that time and work absolutely paid off.  I know a lot about monitoring, going after the right information when something specific is wrong.  It also gives me a bag of tricks that mostly causes laptops to run out of disk space often.  Disk is cheap though, these days.'
url: /index.php/datamgmt/datadesign/lazy-dba-monitoring-sql-server/
views:
  - 16574
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server Admin
tags:
  - baseline
  - monitoring
  - performance
  - red gate
  - reporting
  - sql response
  - sql server 2008

---
There are hundreds if not thousands of T-SQL Scripts, Powershell Cmdlets, .NET programs and even old vbscript scripts out there to monitor SQL Server and Operating System level performance. I&#8217;ve written hundreds of them myself over the years. Without a doubt, all of that time and work absolutely paid off. It helped me learn even more about monitoring the right things and going after the right information when something specific is wrong. It also gives me a bag of tricks (folder named, BagOfTricks) that mostly causes laptops to run out of disk space often. Disk is cheap these days, right?!

Years ago I realized that writing all those scripts and running them in SSIS, SQL Agent and such was good, but was I being efficient? Yes, I found that being lazy was far out of reach with these methods. Even making those scripts and programs as dynamic and mobile as they could be, there was always the fact that I had to write and update them along with implement them with in some cases, the same amount of work. Not the most efficient way to spend time as a DBA. I mean, business wants you to work for them, not for you. As sad as that is, it often is the fact. In all, writing scripts is a must and you need to in order to learn internals and become more familiar with the architecture of SQL Server and the Operating Systems they are running on. I will always recommend that and real time troubleshooting with all of indicators we retrieve in these ways can never be replaced with anything else. What about the return of investment to the company for overall monitoring? Beyond real time troubleshooting when a problem is found?

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazydba.gif" alt="" title="" width="378" height="378" />
</div>

**Monitoring by Next, Next, Finish**

Recently I was honored to be accepted into the [Friends of Red Gate Program][1]. Red Gate has always been known for being part in the community and giving a lot (and I mean, a lot!) back to us. Having the opportunity to work with them is truly exciting. While being a member of this program, I’ve had the chance to run some Red Gate tools that I previously had not been able to. One of them was, SQL Response. For this Lazy DBA topic, we are going to use [SQL Response][2] to discuss monitoring and using your time more efficiently. First, let’s go over SQL Response briefly to set the stage for why it provides us with more efficient monitoring.

I put SQL Response under the microscope and looked at everything it was doing, reading, using to do so and the impact had on my test database server. The impact was very low (my word) and installation quick and painless. In fact, SQL Response seems to use nothing more but what it has at its fingertips to use from SQL Server. That being normal traces, DMVs and system tables/views. These all are used to gather what it needs to successfully tell you, &#8220;There is a problem&#8221;. As with any great monitoring tool, notifications by email are available in SQL Response.

Most of the batches that SQL Response sent to the DB Server were clean and contained statements I think all of us rely on daily. Things such as the normal check for Agent status, Query Plan Cache and OS performance from the dm_os list of DMV’s. Very well formed as well. 

The meat of SQL Response is there and is just enough to get the job done. I’m the type of DBA that likes thin, easy to read and give me what I want, tools. Leaving as little to no impact on the database servers while it does what I need. This one does that by not adding a lot of &#8220;fluff&#8221; for the graphics and simply does the task is was handed. Of course, everyone wants their reports and there needs some help in that area. When it comes down to it, my worry is telling me when a problem exists before I have to deal with screaming users. All of that without spending a half hour determining which animated gif is telling me know I have a CPU problem.

The following is a snapshot of SQL Response showing my horribly starving disk on my laptop.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/lazydba_monitor.gif" alt="" title="" width="628" height="240" />
</div>

There really isn’t much more to it than a clean and straight forward interface. I like the fact that there is a recommendations section also. This appears to be recommendations that are clear and precise. Most that I forced errors on the test database server showed recommendations that I would recommend as well. That alone shows great thought was put into this good feature.

So where is all of this going? Being efficient in monitoring is as important as the last [topic][3] we discussed. Setting up custom monitoring on your own takes a lot of time and work and while that work is being done, your database server may be in trouble. Enlisting the vendors that supply these monitoring tools and put extreme effort towards making them work for your needs is a way to put automation, real-time notifications and limited impacting tools in place quickly and safely.

**Final Note**

There never really is a last note about monitoring tools. You still have to put work into deciding which one is best for you database servers and your unique methods of confronting the day as a DBA. Choose them wisely and don’t throw something on top of a production database server without knowing exactly what it does, how it does it and if it itself, will cause unwanted problems with performance.

 [1]: http://www.red-gate.com/about/community_relations/friends_of_RG.htm
 [2]: http://www.red-gate.com/products/SQL_Response/index.htm
 [3]: /index.php/DataMgmt/DBAdmin/lazy-dba-sql-trace