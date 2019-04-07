---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 1
author: SQLDenis
type: post
date: 2008-12-05T12:45:59+00:00
ID: 237
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-1/
views:
  - 5420
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday
  - sql server 2008

---
I decided to do a weekly post with the best SQL Server related links of the week. These are articles that I find useful and think that they also might be beneficial to you the reader.

Here we go

[Space Used By Worktables][1]
  
Kalen Delaney explains how to find the amount of space occupied by a worktable by using DMVs

[Defensive database programming: eliminating IF statements][2]
  
Alexander Kuznetsov explains why the following logic can fail 40% of the time
  
IF EXISTS(some query) BEGIN
    
DO SOMETHING;
  
END

[Automating Common DBA Tasks Complete Series][3]
  
Jonathan Kehayias has posted a nice collection of code in this post, some of it is below
  
Configuring SQL Server 2000 Notification with CDOSys
  
Configuring SQL Server 2005/2008 Database Mail
  
Log file growth in SQL Server
  
Monitor free space in the database files
  
Monitor free space on the server hard disks
  
Monitor the SQL Server Error Log
  
Monitor long running SQL Agent Jobs
  
Monitor failed SQL Agent Jobs

[When constraint remains in system table even the table is no longer exists][4]
  
Uri Dimant shows the problem with named constraints and temp tables

[Advanced SQL Server 2005 Express Job Scheduling][5]
  
Mladen Prajdi&#263; writes how to schedule jobs in SQL Server 2005 Express

[BCP Basics][6]
  
Michelle Ufford walks us through the basics of BCP (bulk copy program). BCP is a utility that installs with SQL Server and can assist with large data transfers.

[Getting rid of Instant File Initialization (or enabling it if that strikes your fancy)][7]
  
Denny Cherry tells us about Instant File Initialization. This allows SQL Server to create files of any size without sitting there for minutes or hours (depending on the size of the files).

[SQL Server and Cloud Links for the Week][8]
  
Why reinvent the wheel? Brent Ozar also has a bunch of links on his weekly link post

That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][9] forum or our [Microsoft SQL Server Admin][10] forum**<ins></ins>

 [1]: http://sqlblog.com/blogs/kalen_delaney/archive/2008/11/26/space-used-by-worktables.aspx
 [2]: http://sqlblog.com/blogs/alexander_kuznetsov/archive/2008/11/27/defensive-database-programming-if-statement-vs-where-clause.aspx
 [3]: http://jmkehayias.blogspot.com/2008/12/automating-common-dba-tasks.html
 [4]: http://dimantdatabasesolutions.blogspot.com/2008/12/when-constraint-remains-in-system-table.html
 [5]: http://weblogs.sqlteam.com/mladenp/archive/2008/12/03/Advanced-SQL-Server-2005-Express-Job-Scheduling.aspx
 [6]: http://sqlfool.com/2008/12/bcp-basics
 [7]: http://itknowledgeexchange.techtarget.com/sql-server/getting-rid-of-instant-file-initialization-or-enabling-it-if-that-strikes-your-fancy/#more-269
 [8]: http://www.brentozar.com/archive/2008/12/sql-server-and-cloud-links-for-the-week-3/
 [9]: http://forum.ltd.local/viewforum.php?f=17
 [10]: http://forum.ltd.local/viewforum.php?f=22