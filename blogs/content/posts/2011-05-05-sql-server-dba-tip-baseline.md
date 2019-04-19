---
title: SQL Server DBA Tip 8 – Server Monitoring – Baseline
author: Ted Krueger (onpnt)
type: post
date: 2011-05-05T10:12:00+00:00
ID: 1142
excerpt: 'Troubleshooting any type of problem can be stressful and tedious.  SQL Server performance issues are no exception.  If an instance of SQL Server suddenly shows a spike or immediate bottleneck with one or many resources, determining the cause of that spi&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-baseline/
views:
  - 12341
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Troubleshooting any type of problem can be stressful and tedious.  SQL Server performance issues are no exception.  If an instance of SQL Server suddenly shows a spike or immediate bottleneck with one or many resources, determining the cause of that spike can test any DBA's patience. 

What can make troubleshooting a performance problem in SQL Server more confusing is the lack of a true baseline.  A baseline can be any set of metrics that have been recorded about SQL Server.  Baselines will show normal operating behavior and, more importantly, show a DBA a true problem verses a normal tasking effect of the usage of your database server.

**Conclusion and using a Baseline**

This tip has covered briefly what a baseline is, why to create baselines and some resources to start collecting the information.  What was not covered yet is how to use them.  A baseline has one purpose or answer to a question: Is the current performance of SQL Server abnormal?  With a baseline you can identify:

  * When normal spikes occur in SQL Server resource consumption
  * When wait statistics may show longer waits than normal (wait statistics is a wide range of reasons or resources being used that may make performance slower)
  * What “normal” performance is for given SQL Server instance

When we know the answers to these three items, we can make a more informed decision on when a problem is a problem.  Without a baseline, a 20% average in CPU for an hour timespan may concern us.  That could take time for a DBA to research and troubleshoot, and then start down a path that may end without a solid answer.  In some extreme cases, a DBA may make the decision to start running resource intense operations during normal business hours.  That decision itself could cause a performance problem while the problem it was intended to solve was not a problem at all. 

We can see why a baseline can hold such value.  It is never too late to start collecting baselines for a SQL Server installation.  If your SQL Server(s) have been running for years and you simply know when they run well and may run poorly, you provide additional validity by having data collected, thereby providing hard evidence as to why SQL Server behaves differently at different times.

**Start with SQL Server DMVs**

When you start a position as a DBA or find yourself with SQL Servers that do not have a baseline, there are many tools that you can use to start building a baseline repository.  SQL Server Integration Services coupled with DMVs can pull in periodic snapshots of the state of SQL Server.   After exploring the data captured from the DMVs, use Performance Monitor to collect the remaining metrics to establish a solid baseline which will act as the starting point to identify a true _problem_ in your SQL Server instances. 

When configuring a baseline collection process, ensure that the type of collections match the SQL Server usage guidelines.  If you are using SSRS and SQL Server Engine on the same physical server, add in metrics that are common to SSRS. These may include more network related metrics as SSRS serves requests for reports.  ****

Taking a snapshot of information using the SQL Server DMVs is a sound and quick method to begin collecting baseline data.  One way that you can begin automating and setting this retrieval of collecting DMV information is to use the [Codeplex project SQL DMVStats][1]. This comprehensive project collects a large amount of information and the majority of setup and configuration work has already been for you.  Using the information in the many DMVs that exist on SQL Server holds value outside of these projects that are available.  Running basic collections on specific DMVs (e.g. dm\_exec\_wait_stats every five to ten minutes) can provide useful information regarding times of the day or week where waits are higher than others. 

After collecting internal SQL Server information, setting up Performance Monitor (perfmon) to capture server statistics can be done.  This will collect information about all the resources on the server that will show exactly how well, over a given time period, the server is responding and performing for SQL Server.   The performance counters you choose can be altered slightly given the type of installation you are running.  A beginning list of counters that you can start with, as well as information on setting perfmon up and collecting these, can be found on [BrentOzar.com][2].

**Schedule Baseline Collection**

The last thing you want to do is rely on baselines gathered by manually checking SQL Server here and there.  The collections should be fully automated, and based on reviews of reporting from the collected information.  The [SQL Server Performance Dashboard][3] is a great example of ways to review snapshots of performance metrics.  (Note: the link used is a fix posted by Rob Carrol for 2008.  [Download][4] the SQL Server Performance Dashboard version 2005 first, and then make this change.)

**Third Party Help**

Using the assistance of a third party solution to gather statistics on SQL Server is a good option to explore.  These solutions provide a vast amount of detail about SQL Server performance while freeing time of a DBA to handle other priorities.  Later in this series there will be a tip that revolves around third party software that can assist in monitoring and collection of performance statistics vendor support for SQL Server.  Look for that tip for a more in-depth look at why third party software can assist you in being more efficient with baselines and other aspects of DBA tasks.

**Notable**

A few months before writing this article I attended, presented and help organize [SQL Saturday in Chicago][5].  During that event I sat in on Erin Stellato's ([Twitter][6] | [Blog][7]) session, “[Baseline First, Troubleshooting Second][8]”.  This presentation was excellent and went over this very topic.  Being a strong believer that you can become far more stable and efficient as a DBA by taking the necessary steps, like baselines, first in an environment, I thoroughly enjoyed the session.

If you have the opportunity to have Erin come near your area and she is presenting this session, or any other, I strongly encourage attending.  You will see the tools that this “Tip” did not go in-depth over.  Tools such as, PerfMon, PAL, Profiler, RMLUtils and on were mentioned, used and demonstrated. 

Thanks to Erin and all the other presenters for sharing their knowledge on this and many other topics we otherwise would learn the hard way. 

 [1]: http://sqldmvstats.codeplex.com/
 [2]: http://www.brentozar.com/archive/2006/12/dba-101-using-perfmon-for-sql-performance-tuning/
 [3]: http://blogs.technet.com/b/rob/archive/2009/02/18/performance-dashboard-reports-for-sql-server-2008.aspx
 [4]: http://www.microsoft.com/downloads/en/details.aspx?FamilyID=1d3a4a0d-7e0c-4730-8204-e419218c1efc
 [5]: http://www.sqlsaturday.com/67/eventhome.aspx
 [6]: http://twitter.com/#!/erinstellato
 [7]: http://www.erinstellato.com/
 [8]: http://www.sqlsaturday.com/viewsession.aspx?sat=67&sessionid=3793