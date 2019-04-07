---
title: 'T-SQL Window Functions – Part 4: Analytic Functions'
author: Steve Hughes (DataOnWheels)
type: post
date: 2012-09-25T14:13:00+00:00
excerpt: 'In the final installment of my series on SQL window functions, we will explore using analytic functions.  Analytic functions were introduced in SQL Server 2012 with the expansion of the OVER clause capabilities.  Analytic functions fall in to two primar&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/t-sql-window-functions-part-4/
views:
  - 15869
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
In the final installment of my series on SQL window functions, we will explore using analytic functions. Analytic functions were introduced in SQL Server 2012 with the expansion of the OVER clause capabilities. Analytic functions fall in to two primary categories: values at a position and percentiles. Four of the functions, LAG, LEAD, FIRST\_VALUE and LAST\_VALUE find a row in the partition and returns the desired value from that row. CUME\_DIST and PERCENT\_RANK break the partition into percentiles and return a rank value for each row. PERCENTILE\_CONT and PERCENTILE\_DISC a value at the requested percentile in the function for each row. All of the functions and examples in this blog will only work with SQL Server 2012.
  
Once again, the following CTE will be used as the query in all examples throughout the post:

<code class="codespan">with CTEOrders as<br />
	(select cast(1 as int) as OrderID, cast('3/1/2012' as date) as OrderDate, cast(10.00 as money) as OrderAmt, 'Joe' as CustomerName<br />
	union select 2, '3/1/2012', 11.00, 'Sam'<br />
	union select 3, '3/2/2012', 10.00, 'Beth'<br />
	union select 4, '3/2/2012', 15.00, 'Joe'<br />
	union select 5, '3/2/2012', 17.00, 'Sam'<br />
	union select 6, '3/3/2012', 12.00, 'Joe'<br />
	union select 7, '3/4/2012', 10.00, 'Beth'<br />
	union select 8, '3/4/2012', 18.00, 'Sam'<br />
	union select 9, '3/4/2012', 12.00, 'Joe'<br />
	union select 10, '3/4/2012', 11.00, 'Beth'<br />
	union select 11, '3/5/2012', 14.00, 'Sam'<br />
	union select 12, '3/6/2012', 17.00, 'Beth'<br />
	union select 13, '3/6/2012', 19.00, 'Joe'<br />
	union select 14, '3/7/2012', 13.00, 'Beth'<br />
	union select 15, '3/7/2012', 16.00, 'Sam'<br />
	)<br />
select OrderID<br />
	,OrderDate<br />
	,OrderAmt<br />
	,CustomerName<br />
   from CTEOrders;<br />
</code>

# Position Value Functions: LAG, LEAD, FIRST\_VALUE, LAST\_VALUE

Who has not needed to use values from other rows in the current row for a report or other query? A prime example is needing to know what the last order value was to calculate growth or just show the difference in the results. This has never been easy in SQL Server until now. All of these functions require the use of the OVER clause and the ORDER BY clause. They all use the current row within the partition to find the row at the desired position. 

The LAG and LEAD functions allow you to specify the offset or how many rows to look forward or backward and they support a default value in cases where the value returned would be null. These functions do not support the use of ROWS or RANGE in the OVER clause. The FIRST\_VALUE and LAST\_VALUE allow you to further define the partition using ROWS or RANGE if desired. 

The following example illustrates all of the functions with various variations on the parameters and settings.

_Update 10/19/2012: One of the readers pointed out confusion between column names in the results and the functions used. This discrepancy has been resolved._

<code class="codespan">select OrderID<br />
	,OrderDate<br />
	,OrderAmt<br />
	,CustomerName<br />
	,LAG(OrderAmt) OVER (PARTITION BY CustomerName ORDER BY OrderID) as PrevOrdAmt<br />
	,LEAD(OrderAmt, 2) OVER (PARTITION BY CustomerName ORDER BY OrderID) as NextTwoOrdAmt<br />
	,LEAD(OrderDate, 2, '9999-12-31') OVER (PARTITION BY CustomerName ORDER BY OrderID) as NextTwoOrdDtNoNull<br />
	,FIRST_VALUE(OrderDate) OVER (ORDER BY OrderID) as FirstOrdDt<br />
	,LAST_VALUE(CustomerName) OVER (PARTITION BY OrderDate ORDER BY OrderID) as LastCustToOrdByDay<br />
FROM CTEOrders</code>

### Results (with shortened column names):

<table cellspacing="0" cellpadding="0" border="1">
  <tr>
    <td valign="top" width="52">
      <p>
        <b>ID</b>
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        <b>OrderDate</b>
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        <b>Amt</b>
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        <b>Cust</b>
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        <b>PrevOrdAmt</b>
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        <b>NextTwoAmt</b>
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        <b>NextTwoDt</b>
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        <b>FirstOrd</b>
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        <b>LastCust</b>
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        1
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        NULL
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        3/3/2012
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Joe
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        2
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        NULL
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Sam
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        3
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        NULL
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Beth
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        4
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Joe
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        5
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        3/5/2012
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Sam
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        6
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/3/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        19
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Joe
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        7
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Beth
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        8
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        16
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Sam
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        9
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        NULL
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12/31/9999
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Joe
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Beth
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/5/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        NULL
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12/31/9999
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Sam
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        NULL
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12/31/9999
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Beth
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        19
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        NULL
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12/31/9999
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Joe
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        NULL
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12/31/9999
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Beth
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="52">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="99">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="53">
      <p>
        16
      </p>
    </td>
    
    <td valign="top" width="60">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="120">
      <p>
        NULL
      </p>
    </td>
    
    <td valign="top" width="107">
      <p>
        12/31/9999
      </p>
    </td>
    
    <td valign="top" width="89">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="90">
      <p>
        Sam
      </p>
    </td>
  </tr>
</table>

If you really like subselects, you can also mix in some subselects and have a very creative SQL statement. The following statement uses LAG and a subselect to find the first value in a partition. I am showing this to illustrate some more of the capabilities of the function in case you have a solution that requires this level of complexity.

<code class="codespan">select OrderID<br />
	,OrderDate<br />
	,OrderAmt<br />
	,CustomerName<br />
	,LAG(OrderAmt,(select count(*)-1<br />
from CTEOrders c<br />
where c.CustomerName = CTEOrders.CustomerName<br />
 and c.OrderID <= CTEOrders.OrderID) , 0)<br />
OVER (PARTITION BY CustomerName ORDER BY OrderDate, OrderID) as FirstOrderAmt<br />
from CTEOrders</code>

# Percentile Functions: CUME\_DIST, PERCENT\_RANK, PERCENTILE\_CONT, PERCENTILE\_DISC

As I wrap up my discussion on window functions, the percentile based functions were the functions I knew the least about. While I have already used the position value functions above, I have not yet needed to use the percentiles. So, that meant I had to work with them for a while so I could share their usage and have some samples for you to use. 

The key differences in the four function have to do with ranks and values. CUME\_DIST and PERCENT\_RANK return a ranking value while PERCENTILE\_CONT and PERCENTILE\_DISC return data values. 

CUME_DIST returns a value that is greater than zero and lest than or equal to one (>0 and <=1) and represents the percentage group that the value falls into based on the order specified. PERCENT_RANK returns a value between zero and one as well (>= 0 and <=1). However, in PERCENT\_RANK the first group is always represented as 0 whereas in CUME\_DIST it represents the percentage of the group. Both functions return the last percent group as 1. In both cases, as the ranking percentages move from lowest to highest, each group’s percent value includes all of the earlier values in the calculation as well. The following statement shows both of the functions using the default partition to determine the rankings of order amounts within our dataset. <code class="codespan">select OrderID<br />
	,OrderDate<br />
	,OrderAmt<br />
	,CustomerName<br />
	,CUME_DIST() OVER (ORDER BY OrderAmt) CumDist<br />
	,PERCENT_RANK() OVER (ORDER BY OrderAmt) PctRank<br />
from CTEOrders</code>

### Results:

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
    
    <td valign="top" width="98">
      <p>
        <b>CumDist</b>
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        <b>PctRank</b>
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
    
    <td valign="top" width="98">
      <p>
        0.2
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
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
    
    <td valign="top" width="98">
      <p>
        0.2
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
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
    
    <td valign="top" width="98">
      <p>
        0.2
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
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
    
    <td valign="top" width="98">
      <p>
        0.33333333
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.214285714
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
    
    <td valign="top" width="98">
      <p>
        0.33333333
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.214285714
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
    
    <td valign="top" width="98">
      <p>
        0.46666667
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.357142857
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
    
    <td valign="top" width="98">
      <p>
        0.46666667
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.357142857
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
    
    <td valign="top" width="98">
      <p>
        0.53333333
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.5
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
    
    <td valign="top" width="98">
      <p>
        0.6
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.571428571
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
    
    <td valign="top" width="98">
      <p>
        0.66666667
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.642857143
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
    
    <td valign="top" width="98">
      <p>
        0.73333333
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.714285714
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
    
    <td valign="top" width="98">
      <p>
        0.86666667
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.785714286
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
    
    <td valign="top" width="98">
      <p>
        0.86666667
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.785714286
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
    
    <td valign="top" width="98">
      <p>
        0.93333333
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        0.928571429
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
    
    <td valign="top" width="98">
      <p>
        1
      </p>
    </td>
    
    <td valign="top" width="133">
      <p>
        1
      </p>
    </td>
  </tr>
</table>

The last two functions, PERCENTILE\_CONT and PERCENTILE\_DISC, return the value at the percentile requested. PERCENTILE\_CONT will return the true percentile value whether it exists in the data or not. For instance, if the percentile group has the values 10 and 20, it will return 15. If PERCENTILE\_DISC, is applied to the same group it will return 10. It will return the smallest value in the percentile group, which in this case is 10. Both functions ignore NULL values and do not use the ORDER BY, ROWS, or RANGE clauses with the PARTITION BY clause. Instead, WITHIN GROUP is introduced which must contain a numeric data type and ORDER BY clause. Only one column can be specified here. Both functions need a percentile value which can be between 0.0 and 1.0. 

The following script illustrates a couple of variations. The first two functions return the median of the default partition. Then next two return the median value for each day. Finally, the last two functions return the low and high values within the partition. The values segmented by the date partition highlight the key difference between the two functions.

<code class="codespan">select OrderID as ID<br />
	,OrderDate as ODt<br />
	,OrderAmt as OAmt<br />
	,CustomerName as CName<br />
	,PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY OrderAmt) OVER () PerCont05<br />
	,PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY OrderAmt) OVER () PerDisc05<br />
	,PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY OrderAmt) OVER (PARTITION BY OrderDate) PerContDt<br />
	,PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY OrderAmt) OVER (PARTITION BY OrderDate) PerDiscDt<br />
	,PERCENTILE_CONT(0) WITHIN GROUP (ORDER BY OrderAmt) OVER() PerCont0<br />
from CTEOrders</code>

### Results

<table cellspacing="0" cellpadding="0" border="1">
  <tr>
    <td valign="top" width="27">
      <p>
        <b>ID</b>
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        <b>ODt</b>
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        <b>OAmt</b>
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        <b>CName</b>
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        <b>PerCont05</b>
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        <b>PerDisc05</b>
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        <b>PerContDt</b>
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        <b>PerDiscDt</b>
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        <b>PerCont0</b>
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        1
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        10.5
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        10.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        2
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/1/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        10.5
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        10.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        3
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        15.0
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        15.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        4
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        15.0
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        15.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        5
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/2/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        15.0
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        15.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        6
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/3/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        12.0
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        12.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        7
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        11.5
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        11.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        10
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        11.5
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        11.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        9
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        11.5
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        11.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        8
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/4/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        18
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        11.5
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        11.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        11
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/5/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        14.0
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        14.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        12
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        17
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        18.0
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        17.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/6/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        19
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Joe
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        18.0
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        17.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        14
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Beth
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        14.5
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
  
  <tr>
    <td valign="top" width="27">
      <p>
        15
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        3/7/2012
      </p>
    </td>
    
    <td valign="top" width="52">
      <p>
        16
      </p>
    </td>
    
    <td valign="top" width="63">
      <p>
        Sam
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        13
      </p>
    </td>
    
    <td valign="top" width="82">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="87">
      <p>
        14.5
      </p>
    </td>
    
    <td valign="top" width="83">
      <p>
        13.00
      </p>
    </td>
    
    <td valign="top" width="77">
      <p>
        10
      </p>
    </td>
  </tr>
</table>

As I wrap up this post, I have to give a shout out to my daughter, Kristy, who is an honors math student. She helped me get my head around this last group of functions. Her honors math work and some statistical work she had done in science helped provide additional insight into the math behind the functions. (Kristy – you rock!)

# Series Wrap Up

I hope this series helps everyone understand the power and flexibility in the window functions made available in SQL Server 2012. If you happen to use Oracle, I know that many of these functions or there equivalent are also available in 11g and they also appear to be in 10g. I have to admit my first real production usage was with Oracle 11g but has since used them with SQL Server 2012. The expanded functionality in SQL Server 2012 is just one more reason to upgrade to the latest version.