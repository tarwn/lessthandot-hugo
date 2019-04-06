---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 7
author: SQLDenis
type: post
date: 2009-01-16T14:37:56+00:00
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-7/
views:
  - 3714
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Here we are with another super exciting episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show
  
Here is what I found interesting this past week in SQL Land:

**[SQL Server Spatial: EMPTY vs. NULL][1]**
  
Yesterday a friend asked me if it was better to always use a value of GEOMETRYCOLLECTION EMPTY as an alternative to making a geometry/geography column nullable. OGC has its own concept of database NULL, that is [GEOMETRY] EMPTY, where [GEOMETRY] can be any valid geometry subtype (that is &#8216;POINT EMPTY&#8217;, &#8216;LINESTRING EMPTY&#8217;, etc). When I asked why it mattered, he said he was tired of putting ..WHERE&#8230;IS NOT NULL in every UPDATE of SQL Server spatial data type. Sounds like reason for a blog entry.

**[TempDB Monitoring and Troubleshooting: Allocation Bottleneck][2]**
  
This blog continues the discussion on the common issues in TempDB that you may need to troubleshoot. See also the post [here][3] and [here][4]

**[Did you know, Dropping a snapshot flushes procedure cache in SQL Server 2005][5]**
  
Snapshot feature was one of the new features that were implemented in SQL Server 2005. When you drop a snapshot created on a source database in SQL Server 2005, it clears the entire procedure cache on the server.

**[The Lesser Known Features of SQL Server Management Studio – Part 3][6]**
  
Aaron Alton shows us a couple of more lesser know features of SSMS, continued here: http://feeds.feedburner.com/~r/TheHobt/~3/513743387/lesser-known-features-of-sql-server_15.html

**[Do you focus too much on your backups???][7]**
  
Mike shows some best practices in regards to backups

**[How I Get By Without sysadmin][8]**
  
Jeremiah Peschka shows you how to get things done even without sysadmin access

**[Time to Rant : Applications and Administrator Rights][9]**
  
Jonathan Kehayias complains/rants: It is 2009, and I am shocked to find that applications today still show up with a requirement to be a sysadmin on SQL Server, and in the case of one particular application I am helping troubleshoot today, a Local Administrator on the SQL Server machine

**[Does the down economy have an impact on your job?][10]**
  
Kevin Kline ask if the down economy has an impact on your job? Leave him a comment.

**[Building Scalable Databases: Pros and Cons of Various Database Sharding Schemes][11]**
  
Dare Obasanjo has an interesting post about the Pros and Cons of Various Database Sharding Scheme



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][12] forum or our [Microsoft SQL Server Admin][13] forum**<ins></ins>

 [1]: http://www.sqlskills.com/BLOGS/BOBB/post.aspx?id=11f903a9-1264-4f0d-aab7-b6ba506ab10a
 [2]: http://blogs.msdn.com/sqlserverstorageengine/archive/2009/01/11/tempdb-monitoring-and-troubleshooting-allocation-bottleneck.aspx
 [3]: http://blogs.msdn.com/sqlserverstorageengine/archive/2009/01/12/tempdb-monitoring-and-troubleshooting-ddl-bottleneck.aspx
 [4]: http://blogs.msdn.com/sqlserverstorageengine/archive/2009/01/12/tempdb-monitoring-and-troubleshooting-out-of-space.aspx
 [5]: http://sankarreddy.spaces.live.com/Blog/cns!1F1B61765691B5CD!319.entry
 [6]: http://feeds.feedburner.com/~r/TheHobt/~3/510530872/lesser-known-features-of-sql-server_12.html
 [7]: http://www.straightpathsql.com/blog/2009/1/14/do-you-focus-too-much-on-your-backups.html
 [8]: http://facility9.com/2009/01/14/how-i-get-by-without-sysadmin/
 [9]: http://jmkehayias.blogspot.com/2009/01/time-to-rant-applications-and.html
 [10]: http://sqlblog.com/blogs/kevin_kline/archive/2009/01/15/does-the-down-econmy-have-an-impact-on-your-job.aspx
 [11]: http://www.25hoursaday.com/weblog/2009/01/16/BuildingScalableDatabasesProsAndConsOfVariousDatabaseShardingSchemes.aspx
 [12]: http://forum.lessthandot.com/viewforum.php?f=17
 [13]: http://forum.lessthandot.com/viewforum.php?f=22