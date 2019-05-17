---
title: Use sys.dm_os_performance_counters to get your Buffer cache hit ratio and Page life expectancy counters
author: SQLDenis
type: post
date: 2010-01-22T13:23:49+00:00
ID: 686
url: /index.php/datamgmt/dbprogramming/use-sys-dm_os_performance_counters-to-ge/
views:
  - 176593
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dmv
  - performance
  - sql server 2000
  - sql server 2005
  - sql server 2008

---
In order to figure out if you need more memory for a SQL Server you can start by taking a look at Buffer cache hit ratio and Page life expectancy.

## Buffer cache hit ratio

Here is what Books On Line has to say about Buffer cache hit ratio
  
_Buffer cache hit ratio
  
Percentage of pages found in the buffer cache without having to read from disk. The ratio is the total number of cache hits divided by the total number of cache lookups over the last few thousand page accesses. After a long period of time, the ratio moves very little. Because reading from the cache is much less expensive than reading from disk, you want this ratio to be high. Generally, you can increase the buffer cache hit ratio by increasing the amount of memory available to SQL Server.
  
_ 

Basically what this means is what is the percentage that SQL Server had the data in cache and did not have to read the data from disk. Ideally you want this number to be as close to 100 as possible.

In order to calculate the Buffer cache hit ratio we need to query the sys.dm\_os\_performance_counters dynamic management view. There are 2 counters we need in order to do our calculation, one counter is Buffer cache hit ratio and the other counter is Buffer cache hit ratio base. We divide _Buffer cache hit ratio_ base by _Buffer cache hit ratio_ and it will give us the Buffer cache hit ratio.
  
Here is the query that will do that, this query will only work on SQL Server 2005 and up.

```sql
SELECT (a.cntr_value * 1.0 / b.cntr_value) * 100.0 as BufferCacheHitRatio
FROM sys.dm_os_performance_counters  a
JOIN  (SELECT cntr_value,OBJECT_NAME 
	FROM sys.dm_os_performance_counters  
  	WHERE counter_name = 'Buffer cache hit ratio base'
        AND OBJECT_NAME = 'SQLServer:Buffer Manager') b ON  a.OBJECT_NAME = b.OBJECT_NAME
WHERE a.counter_name = 'Buffer cache hit ratio'
AND a.OBJECT_NAME = 'SQLServer:Buffer Manager'
```

## Page life expectancy {#PLE}

Now let's look at Page life expectancy.
  
Page life expectancy is the number of seconds a page will stay in the buffer pool, ideally it should be above 300 seconds. If it is less than 300 seconds this could indicate memory pressure, a cache flush or missing indexes.

Here is how to get the Page life expectancy

```sql
SELECT *
FROM sys.dm_os_performance_counters  
WHERE counter_name = 'Page life expectancy'
AND OBJECT_NAME = 'SQLServer:Buffer Manager'
```

What I currently get for the queries is a Page life expectancy of 470333 and the Buffer cache hit ratio is 100. **What I would like you to do is run these 2 queries on your systems and leave me the results in a comment so that we can compare**

Also take a look at how to capture this info if you prefer to run perfmon (or if you are still running SQL Server 2000) by reading this excellent article by Brent Ozar here: [SQL Server Perfmon (Performance Monitor) Best Practices][1]

\*** **Remember, if you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://www.brentozar.com/archive/2006/12/dba-101-using-perfmon-for-sql-performance-tuning/
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22