---
title: Dynamic PIVOT on multiple columns
author: Naomi Nosonovsky
type: post
date: 2011-05-29T14:42:00+00:00
ID: 1193
excerpt: |
  In this blog post I want to discuss a problem I encounter frequently in SQL related forums - how to perform dynamic PIVOT involving multiple columns using pure T-SQL solution.
  
  With the introduction of PIVOT operator in SQL 2005, it became very easy t&hellip;
url: /index.php/datamgmt/datadesign/dynamic-pivot-on-multiple-columns/
views:
  - 44111
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
In this blog post I want to discuss a problem I encounter frequently in SQL related forums &#8211; how to perform dynamic PIVOT involving multiple columns using pure T-SQL solution. In SQL Server Reporting Services this functionality can be easily achieved using [Matrix template][1].

With the introduction of PIVOT operator in SQL 2005, it became very easy to write queries that transform rows into columns. There are numerous articles on the Web as how to perform PIVOT and even dynamic PIVOT. However, most of these articles explain how to perform such queries for just one column. I want to expand the transformation for multiple columns.

The idea of a solution is quite simple &#8211; you need to generate the SQL dynamically. Since we want to use pivot on multiple columns, we need to use [CASE based pivot][2].

You need to keep in mind, that resulting query should have less than 1024 columns. You may build the check for number of columns into the query.

Now, every time I need to create a dynamic query, I need to know what is the final query I am going to arrive to. Having the idea in mind and printing the SQL command until I got it right helps to create the working query.

Let&#8217;s consider the first example based on the AdventureWorks SalesOrderHeader table.
  
This code creates the table we&#8217;re going to transform &#8211; it gives us summary of orders and total due per each quarter:

sql
USE AdventureWorks 

SELECT   DATEPART(quarter,OrderDate) AS [Quarter], 
         DATEPART(YEAR,OrderDate)    AS [Year], 
         COUNT(SalesOrderID)         AS [Orders Count], 
         SUM(TotalDue)               AS [Total Due] 
INTO     #SalesSummary 
FROM     Sales.SalesOrderHeader 
GROUP BY DATEPART(quarter,OrderDate), 
         DATEPART(YEAR,OrderDate) 

SELECT   * 
FROM     #SalesSummary 
ORDER BY [Year], 
         [Quarter]
```

The #SalesSummary table lists count of orders and total due for each quarter and year.

<center>
  </p> 

<div class="tables">
<table border="1" cellpadding ="5" cellspacing = "5"> <caption style="color:blue;font-weight:bold">Quarterly Orders Summary</caption> 

<tr>
<th>
  Quarter
</th>

<th>
  Year
</th>

<th>
  Orders Count
</th>

<th>
  Total Due
</th>
</tr>

<tr>
<td align="right" style="width: 20%">
  3
</td>

<td align="right" style="width: 20%">
  2001
</td>

<td align="right" style="width: 20%">
  621
</td>

<td align="right" style="width: 20%">
  $5,850,932.9483
</td>
</tr>

<tr>
<td align="right">
  4
</td>

<td align="right">
  2001
</td>

<td align="right">
  758
</td>

<td align="right">
  $8,476,619.278
</td>
</tr>

<tr>
<td align="right">
  1
</td>

<td align="right">
  2002
</td>

<td align="right">
  741
</td>

<td align="right">
  $7,379,686.3091
</td>
</tr>

<tr>
<td align="right">
  2
</td>

<td align="right">
  2002
</td>

<td align="right">
  825
</td>

<td align="right">
  $8,210,285.1655
</td>
</tr>

<tr>
<td align="right">
  3
</td>

<td align="right">
  2002
</td>

<td align="right">
  1054
</td>

<td align="right">
  $13,458,206.13
</td>
</tr>

<tr>
<td align="right">
  4
</td>

<td align="right">
  2002
</td>

<td align="right">
  1072
</td>

<td align="right">
  $10,827,327.4904
</td>
</tr>

<tr>
<td align="right">
  1
</td>

<td align="right">
  2003
</td>

<td align="right">
  1091
</td>

<td align="right">
  $8,550,831.8702
</td>
</tr>

<tr>
<td align="right">
  2
</td>

<td align="right">
  2003
</td>

<td align="right">
  1260
</td>

<td align="right">
  $10,749,269.374
</td>
</tr>

<tr>
<td align="right">
  3
</td>

<td align="right">
  2003
</td>

<td align="right">
  4152
</td>

<td align="right">
  $18,220,131.5285
</td>
</tr>

<tr>
<td align="right">
  4
</td>

<td align="right">
  2003
</td>

<td align="right">
  5940
</td>

<td align="right">
  $16,787,382.3141
</td>
</tr>

<tr>
<td align="right">
  1
</td>

<td align="right">
  2004
</td>

<td align="right">
  6087
</td>

<td align="right">
  $14,170,982.5455
</td>
</tr>

<tr>
<td align="right">
  2
</td>

<td align="right">
  2004
</td>

<td align="right">
  6888
</td>

<td align="right">
  $17,969,750.9487
</td>
</tr>

<tr>
<td align="right">
  3
</td>

<td align="right">
  2004
</td>

<td align="right">
  976
</td>

<td align="right">
  $56,178.9223
</td>
</tr></table>
</div>

<p>
</center>
</p>

<p>
Now, suppose we want to see these results horizontally. The following dynamic SQL produces the desired output:
</p>

<pre lang="tsql">

DECLARE  @SQL  NVARCHAR(MAX), 
    @Cols NVARCHAR(MAX)
    
SELECT @Cols = STUFF((select ', 
SUM(CASE WHEN [Quarter]=' + CAST([Quarter] as varchar(3)) + ' AND [Year] = ' + 
CAST([Year] as char(4)) + 
' THEN [Orders Count] ELSE 0 END) AS [' + 
CAST([Year] as char(4)) + '-' + CAST([Quarter] as varchar(3)) + 
' Orders], 
SUM(CASE WHEN [Quarter]=' + CAST([Quarter] as varchar(3)) + 
' AND [Year] = ' + CAST([Year] as char(4)) + 
' THEN [Total Due] ELSE 0 END) AS [' + CAST([Year] as char(4)) + '-' + 
CAST([Quarter] as varchar(3)) + ' Sales]'
FROM #SalesSummary 
ORDER BY [Year],[Quarter] FOR XML PATH(''),type).value('.','varchar(max)'),1,2,'')

SET @SQL = 'SELECT ' + @Cols + '  FROM #SalesSummary' 

--print @SQL 
EXECUTE( @SQL)</pre>

<p>
It produces the transformed result (I show only few first columns):
</p>

<div class="tables">
<table border="1" cellpadding ="6" cellspacing = "4"> <caption style="color:blue;font-weight:bold">Transformed result</caption> 

<tr>
<th>
  2001-3 Orders
</th>

<th>
  2001-3 Sales
</th>

<th>
  2001-4 Orders
</th>

<th>
  2001-4 Sales
</th>

<th>
  2002-1 Orders
</th>

<th>
  2002-1 Sales
</th>

<th>
  2002-2 Orders
</th>

<th>
  2002-2 Sales
</th>
</tr>

<tr>
<td align="right">
  621
</td>

<td align="right">
  $5,850,932.9483
</td>

<td align="right">
  758
</td>

<td align="right">
  $8,476,619.278
</td>

<td align="right">
  741
</td>

<td align="right">
  $7,379,686.3091
</td>

<td align="right">
  825
</td>

<td align="right">
  $8,210,285.1655
</td>
</tr></table>
</div>

<p>
You may want to un-comment PRINT @SQL statement if you want to see the generated command. I used XML PATH to concatenate rows into a string based on this blog post by Brad Schulz <a href="http://bradsruminations.blogspot.com/2009/10/making-list-and-checking-it-twice.html">Making a list and checking it twice</a>.
</p>

<p>
Another interesting problem was presented in this <a href="http://social.msdn.microsoft.com/Forums/hu-HU/transactsql/thread/f64ca6b6-9d7b-4863-86fa-591af1ec2b9f">MSDN Transact-SQL forum thread</a>:
</p>

<p>
For the claims table that has many integer columns, transform rows into columns. I demonstrate just the solution with the table creation script:
</p>

<pre lang="tsql">
USE tempdb 

IF OBJECT_ID('Claims','U') IS NOT NULL 
DROP TABLE Claims 

GO 

CREATE TABLE Claims ( 
Claim  INT, 
HCPC   INT, 
[mod]  INT, 
charge INT, 
paid   INT, 
Qty    INT) 

INSERT INTO Claims 
SELECT 12345, 
  99245, 
  90, 
  20, 
  10, 
  1 
UNION ALL 
SELECT 12345, 
  99112, 
  NULL, 
  30, 
  20, 
  1 
UNION ALL 
SELECT 12345, 
  99111, 
  80, 
  50, 
  25, 
  2 
UNION ALL 
SELECT 11112, 
  99911, 
  60, 
  50, 
  20, 
  1 
UNION ALL 
SELECT 12222, 
  99454, 
  NULL, 
  50, 
  20, 
  1 

SELECT * FROM Claims</pre>

<p>
The output is:<br /> 

<center>
</p> 

<div class="tables">
  <table border="1" cellpadding ="5" cellspacing = "5"> <caption style="color:blue;font-weight:bold">Claims table</caption> 
  
  <tr>
    <th>
      Claim
    </th>
    
    <th>
      HCPC
    </th>
    
    <th>
      mod
    </th>
    
    <th>
      Charge
    </th>
    
    <th>
      Paid
    </th>
    
    <th>
      Qty
    </th>
  </tr>
  
  <tr>
    <td align="right" style="width: 20%">
      12345
    </td>
    
    <td align="right" style="width: 20%">
      99245
    </td>
    
    <td align="right" style="width: 20%">
      90
    </td>
    
    <td align="right" style="width: 20%">
      20
    </td>
    
    <td align="right" style="width: 20%">
      10
    </td>
    
    <td align="right" style="width: 20%">
      1
    </td>
  </tr>
  
  <tr>
    <td align="right" style="width: 20%">
      12345
    </td>
    
    <td align="right" style="width: 20%">
      99112
    </td>
    
    <td align="right" style="width: 20%">
      NULL
    </td>
    
    <td align="right" style="width: 20%">
      30
    </td>
    
    <td align="right" style="width: 20%">
      20
    </td>
    
    <td align="right" style="width: 20%">
      1
    </td>
  </tr>
  
  <tr>
    <td align="right" style="width: 20%">
      12345
    </td>
    
    <td align="right" style="width: 20%">
      99111
    </td>
    
    <td align="right" style="width: 20%">
      80
    </td>
    
    <td align="right" style="width: 20%">
      50
    </td>
    
    <td align="right" style="width: 20%">
      25
    </td>
    
    <td align="right" style="width: 20%">
      2
    </td>
  </tr>
  
  <tr>
    <td align="right" style="width: 20%">
      11112
    </td>
    
    <td align="right" style="width: 20%">
      99911
    </td>
    
    <td align="right" style="width: 20%">
      60
    </td>
    
    <td align="right" style="width: 20%">
      50
    </td>
    
    <td align="right" style="width: 20%">
      20
    </td>
    
    <td align="right" style="width: 20%">
      1
    </td>
  </tr>
  
  <tr>
    <td align="right" style="width: 20%">
      12222
    </td>
    
    <td align="right" style="width: 20%">
      99454
    </td>
    
    <td align="right" style="width: 20%">
      NULL
    </td>
    
    <td align="right" style="width: 20%">
      50
    </td>
    
    <td align="right" style="width: 20%">
      20
    </td>
    
    <td align="right" style="width: 20%">
      1
    </td>
  </tr></table>
</div>

<p>
  </center>
</p>

<p>
  And the following code transforms the result (as you see, I am using a loop and ROW_NUMBER() approach):
</p>

<pre lang="tsql">
DECLARE  @SQL     NVARCHAR(MAX), 
    @Loop    INT, 
    @MaxRows INT 

SET @Sql = '' 

SELECT @MaxRows = MAX(MaxRow) 
FROM   (SELECT   COUNT(* ) AS MaxRow, 
            Claim 
  FROM     Claims 
  GROUP BY Claim) X 

SET @Loop = 1 

WHILE @Loop <= @MaxRows 
BEGIN 
SELECT @SQL = @SQL + ',     SUM(CASE WHEN Row = ' + CAST(@Loop AS VARCHAR(10)) + ' THEN ' + QUOTENAME(Column_Name) + ' END) AS [' + COLUMN_NAME + CAST(@Loop AS VARCHAR(10)) + ']'
FROM   INFORMATION_SCHEMA.COLUMNS 
WHERE  TABLE_Name = 'Claims' 
      AND COLUMN_NAME NOT IN ('Row','Claim') 

SET @Loop = @Loop + 1 
END 

--PRINT @SQL 
SET @SQL = 'SELECT Claim' + @SQL + ' FROM (select *,          row_number() over (partition by Claim ORDER BY Claim) as Row         FROM Claims) X GROUP BY Claim ' 

PRINT @SQL 

EXECUTE( @SQL)</pre>

<div class="tables">
  <table border="1" cellpadding ="5" cellspacing = "5"> <caption style="color:blue;font-weight:bold">Transformed Output</caption> 
  
  <tr>
    <th>
      Claim
    </th>
    
    <th>
      HCPC1
    </th>
    
    <th>
      mod1
    </th>
    
    <th>
      Charge1
    </th>
    
    <th>
      Paid1
    </th>
    
    <th>
      Qty1
    </th>
    
    <th>
      HCPC2
    </th>
    
    <th>
      mod2
    </th>
    
    <th>
      Charge2
    </th>
    
    <th>
      Paid2
    </th>
    
    <th>
      Qty2
    </th>
    
    <th>
      HCPC3
    </th>
    
    <th>
      mod3
    </th>
    
    <th>
      Charge3
    </th>
    
    <th>
      Paid3
    </th>
    
    <th>
      Qty3
    </th>
  </tr>
  
  <tr>
    <td align="right" style="width: 20%">
      11112
    </td>
    
    <td align="right" style="width: 10%">
      99911
    </td>
    
    <td align="right" style="width: 10%">
      60
    </td>
    
    <td align="right" style="width: 10%">
      50
    </td>
    
    <td align="right" style="width: 10%">
      20
    </td>
    
    <td align="right">
      1
    </td>
    
    <td align="right" style="width: 20%">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right" style="width: 20%">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
  </tr>
  
  <tr>
    <td align="right" style="width: 20%">
      12222
    </td>
    
    <td align="right">
      99454
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      50
    </td>
    
    <td align="right">
      20
    </td>
    
    <td align="right">
      1
    </td>
    
    <td align="right" style="width: 20%">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      NULL
    </td>
  </tr>
  
  <tr>
    <td align="right" style="width: 20%">
      12345
    </td>
    
    <td align="right">
      99245
    </td>
    
    <td align="right">
      90
    </td>
    
    <td align="right">
      20
    </td>
    
    <td align="right">
      10
    </td>
    
    <td align="right">
      1
    </td>
    
    <td align="right" style="width: 20%">
      99112
    </td>
    
    <td align="right">
      NULL
    </td>
    
    <td align="right">
      30
    </td>
    
    <td align="right">
      20
    </td>
    
    <td align="right">
      1
    </td>
    
    <td align="right">
      99111
    </td>
    
    <td align="right">
      80
    </td>
    
    <td align="right">
      50
    </td>
    
    <td align="right">
      25
    </td>
    
    <td align="right">
      2
    </td>
  </tr></table>
</div>

<p>
  I&#8217;ll post another recent problem found in the following MSDN thread <a href="http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/89d5954f-d518-4eb4-8c44-c124c6e1ee6d">Basic Crosstab Query</a>
</p>

<pre lang="tsql">
SET NOCOUNT ON;
GO
USE tempdb;
GO
CREATE TABLE #T (
StoreID char(5) NOT NULL,
CriteriaNo int NOT NULL,
Result int NOT NULL, 
Position int NOT NULL,
PRIMARY KEY (StoreID, CriteriaNo)
);
GO
INSERT INTO #T(StoreID, CriteriaNo, Result, Position)
SELECT '0001', 9, 10, 1 UNION ALL 
SELECT '0002', 9, 12, 2 UNION ALL 
SELECT '0001', 10, 5, 1 UNION ALL
SELECT '0002', 10, 6, 2;
GO
-- dynamic
DECLARE @sql nvarchar(MAX), @Cols nvarchar(max);

SELECT @Cols = (select ', ' + 'MAX(case when CriteriaNo = ' + CONVERT(varchar(20), CriteriaNo) + 
' then Result else 0 end) AS CriteriaNo' + CONVERT(varchar(20), CriteriaNo) + 
', MAX(case when CriteriaNo = ' + CONVERT(varchar(20), CriteriaNo) + 
' then Position else 0 end) AS CriteriaPosition' + CONVERT(varchar(20), CriteriaNo)
from (select distinct CriteriaNo from #T) X ORDER By CriteriaNo 
FOR XML PATH(''))

SET @sql = 'SELECT StoreID' + @Cols + ', SUM(Result) as PerformancePrint
FROM #T
GROUP BY StoreID'

EXECUTE(@sql)

</pre>

<p>
  See also my TechNet WiKi article on this same topic with more code samples
</p>

<p>
  <a href="http://social.technet.microsoft.com/wiki/contents/articles/17510.t-sql-dynamic-pivot-on-multiple-columns.aspx">T-SQL: Dynamic Pivot on Multiple Columns</a>
</p>

<p>
  *** <strong>Remember, if you have a SQL related question, try our <a href="http://forum.ltd.local/viewforum.php?f=17">Microsoft SQL Server Programming</a> forum or our <a href="http://forum.ltd.local/viewforum.php?f=22">Microsoft SQL Server Admin</a> forum</strong><ins></ins>
</p>

 [1]: http://msdn.microsoft.com/en-us/library/ms157334(SQL.100).aspx
 [2]: /index.php/DataMgmt/DataDesign/understanding-sql-server-2000-pivot