---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 6
author: SQLDenis
type: post
date: 2009-01-09T13:21:21+00:00
ID: 277
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-6/
views:
  - 4805
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Here we are with another super fascinating episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show
  
Here is what I found interesting this past week in SQL Land:

**[WMI Provider Error: Access is denied. [0x80070005] from SQL Server Computer Manager][1]**
  
Explanation of the following: When you attempt to change the Login Account for the SQL services [SQL Server, SQL Agent, Full Text, Analysis Services] from the SQL Server Computer Manager tool, you may encounter the following error message:
  
"WMI Provider Error"
  
"Access is denied. [0x80070005]"

**[What is allocation bottleneck?][2]**
  
Allocation bottleneck refers to contention in the system pages that store allocation structures. Sunil Agarwal explains what allocation bottleneck is

**[
  
Installing SQL Server 2005 SP3 or SQL Server 2008 on builds prior to XP SP3 Can lead to Bug Check (0xF4, Blue screen, Critical Process Termination)][3]**Due to a bug in Windows XP builds prior to the XP SP2 hot fix or XP SP3 release you may encounter one of the following bugs while attempting to install SQL Server 2008 or SQL Server 2005 SP3 based builds.

**[Error: 5123, Severity: 16, State: 1 when moving TempDB][4]**
  
Jonathan Kehayias explains how to move tempdb

**[Seek or scan?][5]**
  
One very common question that I see on the forums is on index seeks and index scans. A query is resulting in a table/clustered index scan, even though there's an index on one or more of the columns been searched on. Gail Shaw does a nice explanation

**[Don't Touch that Shrink Button!][6]**
  
This topic has indeed been done to death. Yet I still often encounter unecessary database autoshrinks, scheduled shrink jobs and at times a seeming lack of knowledge on just what one should be doing as far as their database sizes.

**[The first pillar – A Coherent Design][7]**
  
One of the definitions on wiktionary.org for coherence is "a logical arrangements of parts". In my initial post, I defined "coherent" for database designs in the following manner: cohesive, comprehendible, standards based, names/datatypes all make sense, needs little documentation. Louis Davidson explains Coherent Design

**[The Lesser Known Features of SQL Server Management Studio (Part 1)][8]**
  
Aaron Alton shows us some handy features in SQL Server Management Studio

**[Do your servers take for ever to reboot?][9]**
  
Denny Cherry explains what caused this to happen



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][10] forum or our [Microsoft SQL Server Admin][11] forum**<ins></ins>

 [1]: http://blogs.msdn.com/psssql/archive/2009/01/05/wmi-provider-error-access-is-denied-0x80070005-from-sql-server-computer-manager.aspx
 [2]: http://blogs.msdn.com/sqlserverstorageengine/archive/2009/01/04/what-is-allocation-bottleneck.aspx
 [3]: http://blogs.msdn.com/psssql/archive/2009/01/07/installing-sql-server-2005-sp3-or-sql-server-2008-on-builds-prior-to-xp-sp3-can-lead-to-bug-check-0xf4-blue-screen-critical-process-termination.aspx
 [4]: http://jmkehayias.blogspot.com/2009/01/error-5123-severity-16-state-1-when.html
 [5]: http://feeds.feedburner.com/~r/SqlInTheWild/~3/506593686/
 [6]: http://www.straightpathsql.com/blog/2009/1/6/dont-touch-that-shrink-button.html
 [7]: http://sqlblog.com/blogs/louis_davidson/archive/2009/01/08/the-first-pillar-a-coherent-design.aspx
 [8]: http://feeds.feedburner.com/~r/TheHobt/~3/505937419/lesser-known-features-of-sql-server.html
 [9]: http://itknowledgeexchange.techtarget.com/sql-server/do-your-servers-take-for-ever-to-reboot/
 [10]: http://forum.lessthandot.com/viewforum.php?f=17
 [11]: http://forum.lessthandot.com/viewforum.php?f=22