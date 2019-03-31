---
title: Using OFFSET N ROWS FETCH NEXT N ROWS ONLY In SQL Server Denali for easy paging
author: SQLDenis
type: post
date: 2010-11-09T16:23:56+00:00
excerpt: |
  SQL Server Denali makes it much easier to do paging compared to previous versions of SQL Server. In SQL Server 2005 or 2008 you would do something like the following to skip the first 50 rows
  
  WITH cte AS (SELECT ROW_NUMBER() OVER(ORDER BY number) AS&hellip;
url: /index.php/datamgmt/dbprogramming/using-offset-n-rows-fetch-next-n-rows-on/
views:
  - 29137
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
SQL Server Denali makes it much easier to do paging compared to previous versions of SQL Server. In SQL Server 2005 or 2008 you would do something like the following to skip the first 50 rows

<pre>WITH cte AS (SELECT ROW_NUMBER() OVER(ORDER BY number) AS ROW,* 
FROM master..spt_values
WHERE TYPE ='P'
) 

SELECT * FROM cte
WHERE ROW BETWEEN 51 AND 100</pre>

Take a look at how you do this in SQL Server Denali, it is much easier and cleaner in my opinion

<pre>SELECT * 
FROM master..spt_values
WHERE type ='P'
ORDER BY number
OFFSET 50 ROWS FETCH NEXT 50 ROWS ONLY;</pre>

This syntax also works with parameters, here is the same code using a parameter for both the SQL Server Denali and 2005/2008 version.

<pre>DECLARE @i int = 51;

WITH cte AS (SELECT ROW_NUMBER() OVER(ORDER BY number) AS ROW,* 
FROM master..spt_values
WHERE TYPE ='P'
) 

SELECT * FROM cte
WHERE ROW BETWEEN @i AND (@i * 2) -1</pre>

<pre>DECLARE @i int = 50

SELECT * 
FROM master..spt_values
WHERE type ='P'
ORDER BY number
OFFSET @i ROWS FETCH NEXT  @i ROWS ONLY;</pre>

If you want all rows after skipping the first 50 rows, you would do something like this in a version pre SQL Server Denali

<pre>DECLARE @i int = 51;

WITH cte AS (SELECT ROW_NUMBER() OVER(ORDER BY number) AS ROW,* 
FROM master..spt_values
WHERE TYPE ='P'
) 

SELECT * FROM cte
WHERE ROW &gt;= @i </pre>

In SQL Server Denali it becomes very simple, all you need to do is OFFSET N ROWS

<pre>DECLARE @i int = 50

SELECT * 
FROM master..spt_values
WHERE type ='P'
ORDER BY number
OFFSET @i ROWS;</pre>

So that was just a quick look at OFFSET.
  
Click on the [SQL Server Denali][1] tag to see all our SQL Server Denali related posts

 [1]: /index.php/All/sql+server+denali: