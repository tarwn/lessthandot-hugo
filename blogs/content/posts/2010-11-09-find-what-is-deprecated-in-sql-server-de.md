---
title: Find what is deprecated in SQL Server Denali by using sys.dm_os_performance_counters
author: SQLDenis
type: post
date: 2010-11-09T16:27:38+00:00
ID: 940
excerpt: |
  With every version of SQL Server come warnings about features/code that is deprecated. You can use the sys.dm_os_performance_counters dynamic management view to find what these deprecated features are.
  
   If you run the following query on SQL Server De&hellip;
url: /index.php/datamgmt/dbprogramming/find-what-is-deprecated-in-sql-server-de/
views:
  - 6189
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - ctp
  - database
  - sql server denali

---
With every version of SQL Server come warnings about features/code that is deprecated. You can use the sys.dm\_os\_performance_counters dynamic management view to find what these deprecated features are.

If you run the following query on SQL Server Denali CTP 1

sql
select * from sys.dm_os_performance_counters
where object_name like '%deprecated%'
```

You get back 239 rows, below are the new things compared to 2008

sp_configure 'awe enabled'
  
sp_configure 'affinity64 mask'
  
sp_configure 'affinity mask'
  
SET ERRLVL
  
SET FMTONLY ON
  
sp_dropsrvrolemember
  
sp_addsrvrolemember
  
sp_droprolemember
  
sp_addrolemember
  
sp_changedbowner
  
DBCC_IND
  
DBCC_EXTENTINFO
  
Old NEAR Syntax
  
NOLOCK or READUNCOMMITTED in UPDATE or DELETE 

Take a look at this post [Find Out If You Are Using Deprecated Features In SQL Server 2008][1] if you want the list for SQL Server 2008 

Click on the [SQL Server Denali][2] tag to see all our SQL Server Denali related posts

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/find-out-if-you-are-using-deprecated-fea-2008
 [2]: /index.php/All/sql+server+denali: