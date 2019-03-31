---
title: 'T-SQL Window Functions – Part 1: The OVER Clause'
author: Steve Hughes (DataOnWheels)
type: post
date: 2012-09-06T11:44:00+00:00
excerpt: 'SQL Window functions were introduced in SQL Server 2005.  At the time, only a small set of functionality was available.  Window functions fill a need in the aggregation story for SQL Server.  Window functions allow the developer to use row level aggrega&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/t-sql-window-functions-part1/
views:
  - 21405
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - order by
  - over
  - partition by
  - window function

---
SQL Window functions were introduced in SQL Server 2005. At the time, only a small set of functionality was available. Window functions fill a need in the aggregation story for SQL Server. Window functions allow the developer to use row level aggregations without the penalty of using cursors to accomplish the same task. Window functions allow you to segment a set of rows and then apply a function to that set of rows. In many cases, you may choose an aggregation function. However, other functions are also available including ranking and analytic functions. In this four-part series, I will start by breaking apart the OVER clause which is the key to understanding window functions in SQL Server. The following posts will expand on each group of window functions which use the OVER clause – ranking, aggregate, and analytic.

Up until 2008 R2, SQL Server only supported a subset of window functions which focused on ranking and some aggregation functions. With SQL Server 2012, Microsoft greatly expanded its support for window functions in T-SQL thus making fairly complex operations such as running totals much simpler to accomplish. 

The following CTE will be used as the query in all examples throughout the post:
  
<code class="codespan">with CTEOrders as&lt;br />
	(select cast(1 as int) as OrderID, cast('3/1/2012' as date) as OrderDate, cast(10.00 as money) as OrderAmt, 'Joe' as CustomerName&lt;br />
	union select 2, '3/1/2012', 11.00, 'Sam'&lt;br />
	union select 3, '3/2/2012', 10.00, 'Beth'&lt;br />
	union select 4, '3/2/2012', 15.00, 'Joe'&lt;br />
	union select 5, '3/2/2012', 17.00, 'Sam'&lt;br />
	union select 6, '3/3/2012', 12.00, 'Joe'&lt;br />
	union select 7, '3/4/2012', 10.00, 'Beth'&lt;br />
	union select 8, '3/4/2012', 18.00, 'Sam'&lt;br />
	union select 9, '3/4/2012', 12.00, 'Joe'&lt;br />
	union select 10, '3/4/2012', 11.00, 'Beth'&lt;br />
	union select 11, '3/5/2012', 14.00, 'Sam'&lt;br />
	union select 12, '3/6/2012', 17.00, 'Beth'&lt;br />
	union select 13, '3/6/2012', 19.00, 'Joe'&lt;br />
	union select 14, '3/7/2012', 13.00, 'Beth'&lt;br />
	union select 15, '3/7/2012', 16.00, 'Sam'&lt;br />
	)&lt;br />
select OrderID&lt;br />
	,OrderDate&lt;br />
	,OrderAmt&lt;br />
	,CustomerName&lt;br />
   from CTEOrders;&lt;br />
</code>

# Defining the Window with the OVER Clause

The purpose of the OVER clause is to define the window over which the function will be applied. The default functionality is to define the window for the entire table. As shown in the example below, the result is the same as if you had used an aggregation function without a GROUP BY clause. Some of what makes OVER unique includes the fact different windows can be specified in a single SELECT statement as will be shown in later sections. 

<code class="codespan">...&lt;br />
select CustomerName&lt;br />
	,SUM(OrderAmt) OVER () as NoParm&lt;br />
   from CTEOrders;&lt;br />
</code>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/NoParmResults.jpg?mtime=1346902398"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/NoParmResults.jpg?mtime=1346902398" width="301" height="239" /></a>
</div>

The OVER clause takes three additional arguments to change the scope of the clause &#8212; PARTITION BY, ORDER BY, and ROWS or RANGE. 

## PARTITION BY

The PARTITION BY clause is used to reduce the scope of the window to which the aggregation applies. This clause partitions the default or entire result set into partitions based on the criteria specified. As a BI developer who has worked a lot with MDX and cubes this is the same concept as a slice. The function will be applied to each partition independently. At this point, we begin to see the real power of window functions. While similar to the GROUP BY clause, the PARTITION BY clause allows you to specify different partitions within the same select statement. The following example builds on the illustration above with no partition specified and a partition on the CustomerName field.

<code class="codespan">select CustomerName&lt;br />
	,SUM(OrderAmt) OVER () as NoParm&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY CustomerName) as PartByName&lt;br />
   from CTEOrders;&lt;br />
</code>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/PartitionByResults.jpg?mtime=1346902402"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/PartitionByResults.jpg?mtime=1346902402" width="474" height="365" /></a>
</div>

## ORDER BY

The ORDER BY clause is used to order how the partitions apply the window function. For instance, when an ORDER BY is present in our current illustration, it will sum all of Beth’s order amounts, but will then add Joe’s to the total as he is the next in order. In the following illustration, no partition is specified so the partition is still the whole table. You can now see how partitions and ordering affect the window function. 

<code class="codespan">select CustomerName&lt;br />
	,SUM(OrderAmt) OVER () as NoParm&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY CustomerName) as PartByName&lt;br />
	,SUM(OrderAmt) OVER (ORDER BY CustomerName) as OrderByName&lt;br />
   from CTEOrders;&lt;br />
</code>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/OrderByResults.jpg?mtime=1346902400"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/OrderByResults.jpg?mtime=1346902400" width="589" height="367" /></a>
</div>

As you can see, if no partition is specified the order will apply to the entire result set or the default partition. This is particularly important to understand when using ranking functions with the OVER clause. Before SQL Server 2012, the ORDER BY clause could only be used with ranking functions. With the release of SQL Server 2012, the ORDER BY clause can now be used with ranking, aggregate, and analytic functions.

The following image visually illustrates the how the PARTITION BY and ORDER BY clauses aggregate and rank data.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/PartitionIllustration.JPG?mtime=1346938792"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/PartitionIllustration.JPG?mtime=1346938792" width="1220" height="506" /></a>
</div>

## ROWS or RANGE

The final clauses that can be used to define a partition are the ROWS and RANGE functions. This functionality was introduced in SQL Server 2012. These functions will specify a set of rows based on the position of the current row and as a result both functions require the use of the ORDER BY clause. Because each partition is anchored to the current row, the rows or range specifications are based on their proximity to the current row. A number of key words can be used with this clause to define the window frame used by the window functions. 

• CURRENT ROW: That’s what it means. It identifies the current row as part of the ROWS or RANGE. This key word is supported by ROWS and RANGE.
  
• UNBOUNDED PRECEDING: Go to the beginning of the partition. This is supported by both ROWS and RANGE.
  
• UNBOUNDED FOLLOWING: Go to the end of the partition. This is supported by both ROWS and RANGE.
  
• _n_ PRECEDING: This is used to specify the number of rows before the current row. This is only supported by the ROWS clause.
  
• _n_ FOLLOWING: This is used to specify the number of rows after the current row. This is only supported by the ROWS clause.

<code class="codespan">select CustomerName&lt;br />
	,SUM(OrderAmt) OVER () as NoParm&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY CustomerName) as PartByName&lt;br />
	,SUM(OrderAmt) OVER (ORDER BY CustomerName) as OrderByName&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY CustomerName ORDER BY OrderDate, OrderID RANGE BETWEEN UNBOUNDED PRECEDING and CURRENT ROW) as RangeByDate&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY CustomerName ORDER BY OrderDate, OrderID ROWS BETWEEN 1 FOLLOWING and 2 FOLLOWING) as NextTwoAmts&lt;br />
   from CTEOrders;&lt;br />
</code>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/RowRangeResults.jpg?mtime=1346902407"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/RowRangeResults.jpg?mtime=1346902407" width="936" height="371" /></a>
</div>

A couple of other notes regarding ROWS and RANGE. In cases where the partition returns no rows, the value returned is NULL by default as seen in rows 5 and 10 above. Also, if the partition only returns fewer rows than specified it will only apply to rows within the range. In the example above, row 4 NextTwoAmts column will only return the sum of row 5 as it is the only row included in the partition. 

Finally, ROWS and RANGE react differently when only the CURRENT ROW is specified. When CURRENT ROW is used by itself with the RANGE clause it will apply the function based on the partitioning and ordering. However, when used with the ROWS clause, it will only return the current row regardless of the partition specification.

<code class="codespan">select CustomerName&lt;br />
	,OrderDate&lt;br />
	,OrderAmt&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY CustomerName ORDER BY OrderDate RANGE CURRENT ROW) as RangeRow&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY CustomerName ORDER BY OrderDate ROWS CURRENT ROW) as RowRow&lt;br />
   from CTEOrders;&lt;br />
</code>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/CurrentRowResults.jpg?mtime=1346902397"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/CurrentRowResults.jpg?mtime=1346902397" width="656" height="235" /></a>
</div>

Stay tuned over the next three weeks where I will discuss ranking functions, aggregate functions and analytic functions.