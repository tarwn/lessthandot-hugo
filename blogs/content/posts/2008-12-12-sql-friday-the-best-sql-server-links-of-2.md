---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 2
author: SQLDenis
type: post
date: 2008-12-12T13:10:20+00:00
ID: 249
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-2/
views:
  - 7155
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Here we go, the second week of The Best SQL Server Links Of The Past Week series.
  
It was a slow week this week, didn&#8217;t see a lot of things

**[Performance Impact: file fragmentation and SAN &#8212; Part I][1]
  
[Performance Impact: file fragmentation and SAN &#8212; Part II][2]
  
[Performance Impact: file fragmentation and SAN &#8212; Part III][3]** 
  
Linchi Shea&#8217;s three-part post about performance impact with file fragmentation andthe SAN

**[Defensive database programming: fun with UPDATE.][4]**
  
Alexander Kuznetsov explains that it is well known that UPDATE &#8230; FROM command does not detect ambiguities. Also it well known that ANSI standard UPDATE may perform very poorly and may be difficult to maintain

**[Podcast: Ryan Dunn &#8211; SQL Data Services (SQL in the cloud)][5] (show number 42)**
  
Greg Low&#8217;s podcast with Ryan Dunn about SQL Data Services. Microsoft Senior Technical Evangelist Ryan Dunn discusses SQL Services and SQL Data Services, the future of the cloud based data services, what is currently available and how to get started.

**[TCP Chimney Offload][6]**
  
Do any of these error look familiar?

_\[Microsoft\]\[ODBC SQL Server Driver\][DBNETLIB] General Network error. Check your network documentation</p> 

ERROR \[08S01\] \[Microsoft\][SQL Native Client]Communication link failure

System.Data.SqlClient.SqlException: A transport-level error has occurred when sending the request to the server. (provider: TCP Provider, error: 0 &#8211; An existing connection was forcibly closed by the remote host.)

A significant part of sql server process memory has been paged out. This may result in a performance degradation.</em>

Read Jason Massie&#8217;s post, also check out the link in the edit section

**[How do I view the script of the DDL triggers][7]**
  
Madhivanan shows how to view the script of the DDL triggers

**[Partition Details and Row Counts][8]**
  
Nice post by Dan Guzman showing some code to get some details and the rowcount for partitions by using the DMVs

**[Never Index a BIT?][9]**
  
Indexing a bit column, that is just crazy talk&#8230;&#8230;.or is it? Jason Massie shows some code which will make your decision if this is crazy or not really simple

**[Diagnosing Transaction Log Performance Issues and Limits of the Log Manager][10]**
  
The SQL CAt team published a paper named Diagnosing Transaction Log Performance Issues and Limits of the Log Manager
  
Overview
  
For transactional workloads I/O performance of the writes to the SQL Server transaction log is critical to both throughput and application response time. This document discusses briefly how to determine if I/O to the transaction log file is a performance bottleneck and how to determine if this is storage related; a limitation is due to log manager itself or a combination of the two. Concepts and topics described in this paper apply mainly to SQL Server 2005 and SQL Server 2008.

**[Is LINQ the next OLE DB? &#8220;LINQ-ed&#8221; Server as a rowset source?][11]**
  
Bob Beauchemin writes &#8220;The &#8220;relational database bigots&#8221; I hang out with don&#8217;t like LINQ at all. They hope it would shrivel up in a corner and become part of the fad-technology graveyard. Or they&#8217;re waiting to make big bucks fixing the performance problems they think will ensue&#8230;&#8230;&#8230;&#8230;.&#8221;
  
You can read the rest there and tell me what you think.



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][12] forum or our [Microsoft SQL Server Admin][13] forum**<ins></ins>

 [1]: http://sqlblog.com/blogs/linchi_shea/archive/2008/12/07/performance-impact-file-fragmentation-and-san.aspx
 [2]: http://sqlblog.com/blogs/linchi_shea/archive/2008/12/08/performance-impact-file-fragmentation-and-san-part-ii.aspx
 [3]: http://sqlblog.com/blogs/linchi_shea/archive/2008/12/10/performance-impact-file-fragmentation-and-san-part-iii.aspx
 [4]: http://sqlblog.com/blogs/alexander_kuznetsov/archive/2008/12/08/defensive-database-programming-fun-with-update.aspx
 [5]: http://www.sqldownunder.com/PreviousShows/tabid/98/Default.aspx
 [6]: http://statisticsio.com/Home/tabid/36/articleType/ArticleView/articleId/305/TCP-Chimney-Offload.aspx
 [7]: http://sqlblogcasts.com/blogs/madhivanan/archive/2008/12/11/script-of-ddl-triggers.aspx
 [8]: http://weblogs.sqlteam.com/dang/archive/2008/12/11/Partition-Details-and-Row-Counts.aspx
 [9]: http://statisticsio.com/Home/tabid/36/articleType/ArticleView/articleId/302/Never-Index-a-BIT.aspx
 [10]: http://sqlcat.com/technicalnotes/archive/2008/12/09/diagnosing-transaction-log-performance-issues-and-limits-of-the-log-manager.aspx
 [11]: http://www.sqlskills.com/BLOGS/BOBB/post/Is-LINQ-the-next-OLE-DB-LINQ-ed-Server-as-a-rowset-source.aspx
 [12]: http://forum.ltd.local/viewforum.php?f=17
 [13]: http://forum.ltd.local/viewforum.php?f=22