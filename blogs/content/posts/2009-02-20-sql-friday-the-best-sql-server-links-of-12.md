---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 12
author: SQLDenis
type: post
date: 2009-02-20T13:15:29+00:00
ID: 331
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-12/
views:
  - 4617
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Time for another episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show.
  
Here is what I found interesting this past week in SQL Land:

**[Understanding backups and log-related Trace Flags in SQL Server 2000/2005 and 2008][1]**
  
Kimberly Tripp is giving a play by play from 2000 to 2005/2008 to discuss the differences and what's changed and why those changes were significant. The biggest changes were between 2000 and 2005.

**[Business Rule Enforcement][2]**
  
Louis Davidson explains how to enforce business rules and what the difficulty is

**[LINQ to SQL: The ongoing Developer/DBA debate][3]**
  
Jonathan Kehayias shows us what he thinks of LINQ to SQL

**[Who is Active? v7.30][4]**
  
Adam Machanic has released the latest version of his _Who is Active_ script. Who is Active? is a comprehensive DMV-based monitoring script, designed to tell you at a glance what processes are active on your SQL Server and what they're up to. It has a number of optional features so that you can get results quickly, or monitor deeply, depending on your needs when you happen to be using the script. 

**[Sp_indexinfo has been updated][5]**
  
Tibor Karaszi has updated his Sp_indexinfo procedure
  
This procedure will give you the following info:

  * What indexes exists for a particular table or for each table in the database
  * Clustered, non-clustered or heap
  * Columns in the index
  * Included columns in the index
  * Unique or nonunique
  * Number rows in the table
  * Space usage
  * How frequently the indexes has been used
  * Any obvious indexes I should add?

**[Digging Around in SQL Internals – View Page Data][6]**
  
Michelle Ufford wrote a post about using DBCC Page and sys.system\_internals\_allocation_units to view page data

**[How to copy DTS 2000 packages between servers (and from SQL 2000 to SQL 2005 and SQL 2008)][7]**

The PSS SQL Server Engineers show us how to migrate SQL Server 2000 DTS packages to a SQL 2005 Server without upgrading them, thus leaving them as legacy DTS 2000 packages within SQL 2005

**[Estimated and Actual execution plan revisited][8]**
  
Gail Shaw explains why the terms 'estimated execution plan' and 'actual execution plan' are perhaps a little bit misleading.



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][9] forum or our [Microsoft SQL Server Admin][10] forum**<ins></ins>

 [1]: http://www.sqlskills.com/BLOGS/KIMBERLY/post/Understanding-the-Transaction-log.aspx
 [2]: http://sqlblog.com/blogs/louis_davidson/archive/2009/02/16/business-rule-enforcement.aspx
 [3]: http://sqlblog.com/blogs/jonathan_kehayias/archive/2009/02/17/linq-to-sql-the-ongoing-developer-dba-debate.aspx
 [4]: http://sqlblog.com/blogs/adam_machanic/archive/2009/02/18/who-is-active-v7-30.aspx
 [5]: http://www.karaszi.com/SQLServer/util_sp_indexinfo.asp
 [6]: http://feedproxy.google.com/~r/SqlFool/~3/J2HQOSyRsf8/
 [7]: http://blogs.msdn.com/psssql/archive/2009/02/19/how-to-copy-dts-2000-packages-between-servers-and-from-sql-2000-to-sql-2005-and-sql-2008.aspx
 [8]: http://feedproxy.google.com/~r/SqlInTheWild/~3/rv7z9KDR6kQ/
 [9]: http://forum.lessthandot.com/viewforum.php?f=17
 [10]: http://forum.lessthandot.com/viewforum.php?f=22