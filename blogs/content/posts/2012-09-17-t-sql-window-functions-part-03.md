---
title: 'T-SQL Window Functions – Part 3: Aggregate Functions'
author: Steve Hughes (DataOnWheels)
type: post
date: 2012-09-17T12:31:00+00:00
excerpt: 'This is part 3 in my series on SQL window functions.  In this post, we will explore using aggregation functions with T-SQL windows.  SQL Server supports most of the aggregation functions such as SUM and AVG in this context with the exceptions of GROUPIN&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/t-sql-window-functions-part-03/
views:
  - 21336
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
This is part 3 in my series on SQL window functions. In this post, we will explore using aggregation functions with T-SQL windows. SQL Server supports most of the aggregation functions such as SUM and AVG in this context with the exceptions of GROUPING and GROUPING_ID. However, prior to SQL Server 2012 only the PARTITION BY clause was supported which greatly limited the usability of aggregate window functions. When support for the ORDER BY clause was introduced in SQL Server 2012, more complex business problems such as running totals could be solved without the extensive use of cursors or nested select statement. In my experience, I used to try various ways to get around this limitation including pushing the data to .NET as it could solve this problem more efficiently. However, this was not always possible when working with reporting. Now that we are able to use SQL to solve the problem, more complex and low-performing solutions can be replaced with these window functions. 

Once again, the following CTE will be used as the query in all examples throughout the post:

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
   from CTEOrders;</code>

# Using PARTITION BY with Aggregate Functions

SQL Server 2005 and the newer versions supports the usage of the PARTITION BY clause by itself. This allowed for some simple aggregate windows. The following example shows SUM and AVG for different partitions of data. The third function actually creates and average using a SUM and COUNT function. 

<code class="codespan">select CustomerName&lt;br />
	,OrderDate&lt;br />
	,OrderAmt&lt;br />
	,SUM(OrderAmt) OVER(PARTITION BY CustomerName) CustomerTotal&lt;br />
	,AVG(OrderAmt) OVER(PARTITION BY OrderDate) AvgDailyAmt&lt;br />
	,CAST(COUNT(OrderID) OVER(PARTITION BY OrderDate) as DECIMAL(8,3)) / CAST(COUNT(OrderID) OVER() as DECIMAL(8,3)) PctOfTotalPerDay&lt;br />
   from CTEOrders&lt;br />
   order by OrderDate;</code>

_NOTE: The COUNT aggregate returns an integer value. In order to return the decimal, the values need to be explicitly converted to decimal types. Otherwise, the result was rounding to zero for all results in this sample._

### Results

<table cellspacing="0" cellpadding="0" border="1">
  <tr>
    <td valign="top" width="125">
      <p>
        <b>CustomerName</b>
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        <b>OrderDate</b>
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        <b>OrderAmt</b>
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        <b>CustomerTotal</b>
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        <b>AvgDailyAmt</b>
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        <b>PctOfTotalPerDay</b>
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        68
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        10.5
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.133333333
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        76
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        10.5
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.133333333
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        76
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.2
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        68
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.2
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        61
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.2
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/3/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        68
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.066666667
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        68
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        12.75
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.266666667
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        61
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        12.75
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.266666667
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        61
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        12.75
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.266666667
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        76
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        12.75
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.266666667
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/5/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        76
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.066666667
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        61
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.133333333
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        19
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        68
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.133333333
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        61
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        14.5
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.133333333
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        16
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        76
      </p>
    </td>
    
    <td valign="top" width="102">
      <p>
        14.5
      </p>
    </td>
    
    <td valign="top" width="142">
      <p>
        0.133333333
      </p>
    </td>
  </tr>
</table>

# Using Subselects

As many of you know, subselect statements in SQL Server are supported, but harder to optimize in SQL Server versus Oracle. Until window functions were introduced all of the items above could be solved by subselects, but performance would degrade as the results needed to work with larger sets of data. With the improved functionality in SQL Server 2012, you should not need to use subselects to return row based aggregations. Besides the performance implications, maintenance will also be much simpler as the SQL becomes more transparent. For reference, here is the subselect syntax to return the same results as above:

<code class="codespan">select cte.CustomerName&lt;br />
	,cte.OrderDate&lt;br />
	,cte.OrderAmt&lt;br />
	,(select SUM(OrderAmt) from CTEOrders where CustomerName = cte.CustomerName) CustomerTotal&lt;br />
	,(select AVG(OrderAmt) from CTEOrders where OrderDate = cte.OrderDate) AvgDailyAmt&lt;br />
	,(select cast(COUNT(OrderID) as DECIMAL(8,3)) from CTEOrders where OrderDate = cte.OrderDate)&lt;br />
		/ (select cast(COUNT(OrderID) as DECIMAL(8,3)) from CTEOrders) AvgDailyAmt&lt;br />
   from CTEOrders cte&lt;br />
   order by cte.OrderDate;</code>

As you can see, while it is possible to solve the same function using the subselects, the code is already getting messier and with data sets larger than what we have here, you would definitely see performance degredation.

# Some Thoughts on GROUP BY

While I am digressing, I wanted to also highlight some details concerning GROUP BY. The one the biggest difficulties working with the GROUP BY clause and aggregates, every column must either be a part of the GROUP BY or have an aggregation associated with it. The window functions help solve this problem as well.
  
In the following examples, the first query returns the sum of the amount by day. This is pretty standard logic when working with aggregated queries in SQL.

<code class="codespan">select OrderDate&lt;br />
	,sum(OrderAmt) as DailyOrderAmt&lt;br />
from CTEOrders&lt;br />
group by OrderDate;</code>

However, if you wanted to see more details, but not include them in the aggregation, the following will not work. 

<code class="codespan">select OrderDate&lt;br />
	,OrderID&lt;br />
	,OrderAmt&lt;br />
	,sum(OrderAmt) as DailyOrderAmt&lt;br />
from CTEOrders&lt;br />
group by OrderDate&lt;br />
	,OrderID&lt;br />
	,OrderAmt;</code>

This SQL statement will reach row individually with the sum at the detail level. You can solve this using the subselect above which is not recommended or you can use a window function. 

<code class="codespan">select OrderDate&lt;br />
	,OrderID&lt;br />
	,OrderAmt&lt;br />
	,sum(OrderAmt) OVER (PARTITION BY OrderDate) as DailyOrderAmt&lt;br />
from CTEOrders</code>

As you can see here and in previous examples the OVER clause allows you to manage the grouping based on the context specified in relationship to the current row.
  
One other twist on the GROUP BY clause. First, I need to give credit to Itzik Ben-Gan for calling this to my attention at one of our Minnesota SQL Server User Group meetings. In his usual fashion he was showing some T-SQL coolness and he showed an interesting error when using the OVER clause with the GROUP BY clause.

The following will return an error because the first expression is an aggregate, but the second expression which is using the OVER clause is not. Also note that in this example the OVER clause is being evaluated for the entire set of data.

<code class="codespan">select sum(OrderAmt)&lt;br />
	, sum(OrderAmt) over() as TotalOrderAmt&lt;br />
from CTEOrders&lt;br />
group by CustomerName</code>

The expression above returns the following error:
  
<span class="MT_red">Column &#8216;CTEOrders.OrderAmt&#8217; is invalid in the select list because it is not contained in either an aggregate function or the GROUP BY clause</span>

The goal of the statement above was to show the customer’s total order amount with the overall order amount. The following statement resolves this issue because it is aggregating the aggregates. The window is now summing the aggregated amount which are grouped on the customer name.

<code class="codespan">select sum(OrderAmt)&lt;br />
	, sum(sum(OrderAmt)) over() as TotalOrderAmt&lt;br />
from CTEOrders&lt;br />
group by CustomerName</code>

Thanks again to Itzik for bringing this problem and resolution to my attention.

# Aggregates with ORDER BY

With the expansion of the OVER clause to include ORDER BY support with aggregates, window functions increased their value substantially. One of the key business problems this allowed us to solve was a running aggregate. 

The first example shows how to get a running total by customer based on date and order ID. 

<code class="codespan">select OrderID&lt;br />
	,OrderDate&lt;br />
	,OrderAmt&lt;br />
	,CustomerName&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY CustomerName ORDER BY OrderDate, OrderID) as RunningByCustomer&lt;br />
   from CTEOrders&lt;br />
   ORDER BY CustomerName, OrderDate;</code>

### Results

<table cellspacing="0" cellpadding="0" border="1">
  <tr>
    <td valign="top" width="67">
      <p>
        <b>OrderID</b>
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        <b>OrderDate</b>
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        <b>OrderAmt</b>
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        <b>CustomerName</b>
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        <b>RunningByCustomer</b>
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        3
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        7
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        20
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        31
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        48
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        61
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        1
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        4
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        25
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        6
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/3/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        37
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        9
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        49
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        19
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        68
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        2
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        11
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        5
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        28
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        8
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        46
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/5/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        60
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        16
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="158">
      <p>
        76
      </p>
    </td>
  </tr>
</table>

This next example is more creative. It begins to show how powerful the window functions are. In this statement, we are going to return the annual running total aggregated by day. The differentiator here is that we use a DATEPART function in the OVER clause to achieve the desired results.

<code class="codespan">select OrderID&lt;br />
	,OrderDate&lt;br />
	,OrderAmt&lt;br />
	,CustomerName&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY datepart(yyyy, OrderDate) ORDER BY OrderDate) as AnnualRunning&lt;br />
   from CTEOrders&lt;br />
   ORDER BY OrderDate;</code>

### Results

<table cellspacing="0" cellpadding="0" border="1">
  <tr>
    <td valign="top" width="67">
      <p>
        <b>OrderID</b>
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        <b>OrderDate</b>
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        <b>OrderAmt</b>
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        <b>CustomerName</b>
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        <b>AnnualRunning</b>
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        1
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        21
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        2
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        21
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        3
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        63
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        4
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        63
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        5
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        63
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        6
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/3/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        75
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        7
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        126
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        8
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        126
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        9
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        126
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        126
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/5/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        140
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        176
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        19
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        176
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        205
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        16
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        205
      </p>
    </td>
  </tr>
</table>

The ORDER BY clause creates an expanding group within the partition. In the examples above, the partition was the customer. Within each partition, ordered groups based on order date and order id are “created”. At each row, the order date and order id groups are aggregated up to the current row’s group thus producing the running total. If more than one row has the same order grouping, all of the rows in the group are aggregated into the total as shown in the second example above with the days and years. 

# Aggregates with ROWS

The ROWS clause is used to further define the partition by specifying which physical rows to include based on their proximity to the current row. As noted in the first post in the series, ROWS requires the ORDER BY clause as this determines the orientation of the partition. 

The following example uses the FOLLOWING keywords to find the next two purchases that the customer made. 

<code class="codespan">select OrderID&lt;br />
	,OrderDate&lt;br />
	,OrderAmt&lt;br />
	,CustomerName&lt;br />
	,SUM(OrderAmt) OVER (PARTITION BY CustomerName ORDER BY OrderDate, OrderID ROWS BETWEEN 1 FOLLOWING and 2 FOLLOWING) as NextTwoAmts&lt;br />
   from CTEOrders&lt;br />
   order by CustomerName, OrderDate, OrderID;</code>

### Results

<table cellspacing="0" cellpadding="0" border="1">
  <tr>
    <td valign="top" width="67">
      <p>
        <b>OrderID</b>
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        <b>OrderDate</b>
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        <b>OrderAmt</b>
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        <b>CustomerName</b>
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        <b>NextTwoAmts</b>
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        3
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        21
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        7
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        28
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        30
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        13
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        NULL
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        1
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        27
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        4
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        24
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        6
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/3/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        31
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        9
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        19
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        19
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        NULL
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        2
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        35
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        5
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        32
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        8
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        30
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/5/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        16
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        16
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="118">
      <p>
        NULL
      </p>
    </td>
  </tr>
</table>

As we noted in the first blog, the last two rows in the partition only contain partial values. For example, order ID 12 contains the sum of only one order, 14, and order ID 14 has now rows following it in the partition and returns NULL as a result. When working with the ROWS clause this must be taken into account. 

# Aggregates with RANGE

Lastly, adding the RANGE to the OVER clause allows you to create aggregates which go to the beginning or end of the partition. RANGE is commonly used with UNBOUNDED FOLLOWING which goes to the end of the partition and UNBOUNDED PRECEDING which goes to the beginning of the partition. One of the most common use would be to specify the rows from the beginning of the partition to the current row which allows for aggregations such as year to date. 

In the example below, we are calculating the average order size over time to the current row. This could be a very effective in a trending report. 

<code class="codespan">select OrderID&lt;br />
	,OrderDate&lt;br />
	,OrderAmt&lt;br />
	,CustomerName&lt;br />
	,AVG(OrderAmt) OVER (ORDER BY OrderID RANGE BETWEEN UNBOUNDED PRECEDING and CURRENT ROW) as AvgOrderAmt&lt;br />
   from CTEOrders&lt;br />
   order by OrderDate;</code>

### Results

<table cellspacing="0" cellpadding="0" border="1">
  <tr>
    <td valign="top" width="67">
      <p>
        <b>OrderID</b>
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        <b>OrderDate</b>
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        <b>OrderAmt</b>
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        <b>CustomerName</b>
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        <b>AvgOrderAmt</b>
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        1
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        2
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        10.5
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        3
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        10.333333
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        4
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        11.5
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        5
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12.6
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        6
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/3/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12.5
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        7
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12.142857
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        8
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12.875
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        9
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12.777777
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12.6
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/5/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12.727272
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        13.083333
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        19
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        13.538461
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        13.5
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="67">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        16
      </p>
    </td>
    
    <td valign="top" width="125">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        13.666666
      </p>
    </td>
  </tr>
</table>

As you can see, the latest versions of OVER clause supports powerful yet simple aggregations which can help in a multitude of reporting and business solutions. Up next, the last blog in the series – Analytic Functions which are all new in SQL Server 2012.