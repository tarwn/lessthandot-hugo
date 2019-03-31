---
title: WHERE not to use FUNCTIONS
author: Axel Achten (axel8s)
type: post
date: 2013-01-24T12:02:00+00:00
excerpt: |
  Functions can be very powerful, but used in the wrong place in a query they can show some unexpected behavior.
  In this post I will be using the AdventureWorks2008R2 database and I will query the Sales.SalesOrderHeader to get all the 2006 OrderDates. A&hellip;
url: /index.php/datamgmt/dbprogramming/where-not-to-use-functions/
views:
  - 5506
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server Admin
tags:
  - function
  - sql server
  - tsql
  - where

---
Functions can be very powerful, but used in the wrong place in a query they can show some unexpected behavior.
  
In this post I will be using the AdventureWorks2008R2 database and I will query the Sales.SalesOrderHeader to get all the 2006 OrderDates. A query that doesn’t make much sense but will return some interesting results.
  
Take the following query:

<pre>USE AdventureWorks2008R2;
GO

SELECT OrderDate FROM Sales.SalesOrderHeader
WHERE OrderDate BETWEEN '20060101' AND '20061231';
GO</pre>

If we take a look at the execution plan:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction1.png?mtime=1359036029"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction1.png?mtime=1359036029" width="767" height="153" /></a>
</div>

We see that there is a complete scan of the Clustered Index which makes sense since there is no index on the OrderDate column.
  
As the Missing Index Hint suggests, create an index on the OrderDate column:

<pre>CREATE INDEX IX_SalesOrderHeader_OrderDate
	ON Sales.SalesOrderHeader(OrderDate);
GO</pre>

Executing our first query again in another form:

<pre>SELECT OrderDate FROM Sales.SalesOrderHeader
WHERE OrderDate &gt;= '20060101' AND OrderDate &lt;='20061231';
GO</pre>

Results in the following Execution Plan:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction2.png?mtime=1359036029"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction2.png?mtime=1359036029" width="771" height="143" /></a>
</div>

You see that the query is internally translated to <code class="codespan">SELECT [OrderDate] FROM [Sales].[SalesOrderHeader] WHERE [OrderDate]&lt;=@1 AND [OrderDate]&lt;=@2&gt;</code> just like the first query using the BETWEEN keyword. The big change is in the execution plan. Since we created an index on the OrderDate column, SQL Server is now using an Index Seek on our index to fetch the results.

Since we are looking for all the dates in 2006, you might want to consider using the YEAR function. The YEAR function returns only the YEAR part of a date(time) value:

<pre>SELECT YEAR('20060127 02:15:59')</pre>

Results in 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction3.png?mtime=1359036029"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction3.png?mtime=1359036029" width="159" height="66" /></a>
</div>

So the following query should make sense and is more readable then the former 2:

<pre>SELECT OrderDate FROM Sales.SalesOrderHeader
WHERE YEAR(OrderDate) = 2006;
GO</pre>

And looking at the result set it makes sense:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction4.png?mtime=1359036029"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction4.png?mtime=1359036029" width="980" height="300" /></a>
</div>

But when we look at the execution plan:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction5.png?mtime=1359036029"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/WhereFunction5.png?mtime=1359036029" width="622" height="149" /></a>
</div>

We see that our index isn’t seeked anymore but gets a complete scan. So instead of searching in some 4K rows, SQL Server is scanning more than 30K of rows. This is because SQL Server is applying the function to all of the rows in our Sales.SalesOrderHeader table before it’s compared to our desired value.

**Conclusion**
  
Be careful when using functions in the WHERE clause of a query. It’s possible that the function will be applied to all the rows before the filter is applied. Resulting in scans, non used indexes, more I/O, memory consumption and a poor performing query.