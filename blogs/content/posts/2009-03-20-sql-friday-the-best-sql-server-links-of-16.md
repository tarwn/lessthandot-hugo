---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 15
author: SQLDenis
type: post
date: 2009-03-20T21:44:45+00:00
ID: 354
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-16/
views:
  - 4211
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Time for another episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show.
  
Here is what I found interesting this past week in SQL Land:

**[A DBA utility database in every SQL Server instance?][1]**
  
Linchi Shea writes: I was reading Louis Davidson’s post earlier today, and what he said below caught my attention:

> I am a big believer in having the database be as self contained as possible, so I try to put [maintenance] objects and such in the database, typically in a schema named utility.

This is different from what I typically do, and got me wondering how people out there managing their maintenance objects. I typically store all the maintenance objects in a dedicated database on every SQL Server instance across the enterprise

**[
  
SQL Server: What is a COLD, DIRTY or CLEAN Buffer?][2]**
  
What is clean buffer? What is cold buffer cache? The PSS SQL Server Engineers explain the difference

**
  
[Set based calculation of products of several numbers][3]**
  
Alexander Kuznetsov shows us how we can use use SUM to calculate the sum of several numbers, but you cannot directly use set-based logic to calculate a product. Yet there is a very simple trick – you can use EXP(SUM(LOG(…))) and get a product of several numbers without a loop



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][4] forum or our [Microsoft SQL Server Admin][5] forum**<ins></ins>

 [1]: http://sqlblog.com/blogs/linchi_shea/archive/2009/03/16/a-dba-utility-database-in-every-sql-server-instance.aspx
 [2]: http://blogs.msdn.com/psssql/archive/2009/03/17/sql-server-what-is-a-cold-dirty-or-clean-buffer.aspx
 [3]: http://sqlblog.com/blogs/alexander_kuznetsov/archive/2009/03/17/set-based-calculation-of-products-of-several-numbers.aspx
 [4]: http://forum.ltd.local/viewforum.php?f=17
 [5]: http://forum.ltd.local/viewforum.php?f=22