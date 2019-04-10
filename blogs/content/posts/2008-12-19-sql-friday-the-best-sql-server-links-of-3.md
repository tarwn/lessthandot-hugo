---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 3
author: SQLDenis
type: post
date: 2008-12-19T15:09:28+00:00
ID: 257
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-3/
views:
  - 6162
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Here we go with the links for week 3

**[Interpreting Output from SQLIOSIM][1]**
  
Kevin Kline has a nice post about interpreting output from SQLIOSIM, he also made a follow up post here: **[More Tidbits on SQLIOSIM][2]**

**[Is Your Internet Activity Hurting Your DBA Career?][3]**
  
Brad McGehee has a post cautioning readers that what you do on the internet can come back to haunt you later on

**[Some interesting affects of Table Partitioning][4]**
  
Kent Tegels wrote a nice post about table partitioning

**[How It Works: SQL Server No Longer Uses RDTSC For Timings in SQL 2008 and SQL 2005 Service Pack 3 (SP3)][5]**
  
The PSS SQL Server Engineers made a post about the change in timings introduced in SQL Server 2005 Service Pack 3.
  
SQL Server 2000: Uses GetTickCount with ~12ms granularity
  
SQL Server 2005: Uses RDTSC with microsecond granularity
  
SQL Server 2005 SP3: Uses interrupt timer with ~1ms granularity
  
SQL Server 2008: Uses interrupt timer with ~1ms granularity

**[Error Handling in T-SQL][6]**
  
Michelle Ufford made a very nice post about error handling, she included a stored proc that you can use to do error logging

**[Spatial Index Diagnostic Procs][7]**
  
Bob Beauchemin made a series of posts about Spatial Index Diagnostic Procs. The links to the articles are below

[Spatial Index Diagnostic Procs – Intro][7]

[Spatial Index Diagnostic Procs – Filters][8]

[Spatial Index Diagnostic Procs – Filter Output][9]

[Spatial Index Diagnostic Procs – How to specify query sample][10]

[Spatial Index Diagnostic Procs – The Rest of the Story][11]

**[SQL Server (2005 and 2008) Trace Flag 1118 (-T1118) Usage][12]**
  
The PSS SQL Server Engineers made a short post some misconceptions that people might have about trace flag 1118 

That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][13] forum or our [Microsoft SQL Server Admin][14] forum**<ins></ins>

 [1]: http://sqlblog.com/blogs/kevin_kline/archive/2008/12/14/interpreting-output-from-sqliosim.aspx
 [2]: http://sqlblog.com/blogs/kevin_kline/archive/2008/12/14/more-tidbits-on-sqliosim.aspx
 [3]: http://www.sqlservercentral.com/blogs/aloha_dba/archive/2008/12/17/is-your-internet-activity-hurting-your-dba-career.aspx
 [4]: http://sqlblog.com/blogs/kent_tegels/archive/2008/12/15/10542.aspx
 [5]: http://blogs.msdn.com/psssql/archive/2008/12/16/how-it-works-sql-server-no-longer-uses-rdtsc-for-timings-in-sql-2008-and-sql-2005-service-pack-3-sp3.aspx
 [6]: http://sqlfool.com/2008/12/error-handling-in-t-sql/
 [7]: http://www.sqlskills.com/BLOGS/BOBB/post/Spatial-Index-Diagnostic-Procs-Intro.aspx
 [8]: http://www.sqlskills.com/BLOGS/BOBB/post/Spatial-Index-Diagnostic-Procs-Filters.aspx
 [9]: http://www.sqlskills.com/BLOGS/BOBB/post/Spatial-Index-Diagnostic-Procs-Filter-Output.aspx
 [10]: http://www.sqlskills.com/BLOGS/BOBB/post/Spatial-Index-Diagnostic-Procs-How-to-specify-query-sample.aspx
 [11]: http://www.sqlskills.com/BLOGS/BOBB/post/Spatial-Index-Diagnostic-Procs-The-Rest-of-the-Story.aspx
 [12]: http://blogs.msdn.com/psssql/archive/2008/12/17/sql-server-2005-and-2008-trace-flag-1118-t1118-usage.aspx
 [13]: http://forum.ltd.local/viewforum.php?f=17
 [14]: http://forum.ltd.local/viewforum.php?f=22