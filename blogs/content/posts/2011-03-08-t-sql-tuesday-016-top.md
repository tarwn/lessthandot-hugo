---
title: 'T-SQL Tuesday #016 – TOP N Percent for the group'
author: Naomi Nosonovsky
type: post
date: 2011-03-08T12:28:00+00:00
ID: 1073
excerpt: |
  This blog post is participating in the T-SQL Tuesday #016
   hosted by Jes Borland
url: /index.php/datamgmt/datadesign/t-sql-tuesday-016-top/
views:
  - 12028
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
This month's T-SQL Tuesday is hosted by [Jes Borland][1] and it is all about grouping and aggregate functions.

 ![T-SQL Tuesday logo][2]

With the introduction of window functions in T-SQL language in SQL 2005, getting top N values per group became a simple task of utilizing [DENSE_RANK()][3] or [RANK()][4] or [ROW_NUMBER()][5] function depending on the business requirements.

However, there is still an important aggregate missing from the group. In particular, PERCENT() aggregate function.

There is a [Connect item][6] in regards to the missing functions.

You may find an article by Itzik Ben-Gan on this topic [Calculate Percentiles][7] very educational.

I become interested in the problem of finding N percent of the group after reading this thread in tek-tips forum [SQL Min 1 of Top 10% and Max 1 of Bottom 10%???][8]

The first idea of approaching this problem is in using NTILE() ranking function. It is as close as we can get, but yet it's not an exactly percentage.

Here is an example that demonstrates discrepancy:

```sql
USE AdventureWorks2008R2 
GO
;with cte as (SELECT p.FirstName, p.LastName
    ,ROW_NUMBER() OVER (ORDER BY s.SalesYTD) AS 'Row Number'
    ,RANK() OVER (ORDER BY s.SalesYTD) AS 'Rank'
    ,DENSE_RANK() OVER (ORDER BY s.SalesYTD) AS 'Dense Rank'
    ,NTILE(10) OVER (ORDER BY s.SalesYTD) AS 'Quartile',
    
    s.SalesYTD, a.PostalCode, a.City 
FROM Sales.SalesPerson s 
    INNER JOIN Person.Person p 
        ON s.BusinessEntityID = p.BusinessEntityID
    INNER JOIN Person.Address a 
        ON a.AddressID = p.BusinessEntityID
)
    
select cte.*, TP.*, LP.* from cte
LEFT join (select top 10 percent FirstName as TPFName, LastName as TPLName, SalesYTD as TPSalesYTD
  from cte Order by SalesYTD ) TP 
on cte.FirstName = TP.TPFName  
and cte.LastName = TP.TPLName 
LEFT join (select top 10 percent FirstName as LPFName, LastName as LPLName, SalesYTD as LPSalesYTD  
from cte Order by SalesYTD DESC) LP 
on cte.FirstName = LP.LPFName  
and cte.LastName = LP.LPLName 
order by cte.[Row Number] 
```
As we can see from this example, we get correctly top 10 percent using NTILE(10) and
  
TOP 10 percent (LEFT JOIN) based on the SalesYTD in the ascending order, but the results from the bottom 10 percent (top 10 percent based on SalesYTD in the descending order) don't match.
  
For the small result sets we can not really get a correct match using [NTILE()][9] as indicated in the Remarks section in BOL.

As we can see in our small set of only 17 records, first 7 groups have 2 records and last 3 groups have only 1 record, that's why our top 10 percent in descending order didn't match.

On the big enough sets the NTILE() is working correctly. Here is an example suggested by Peter Larsson, that demonstrates it:

```sql
;WITH cteRandomData(Value)
AS (
           SELECT     ABS(CHECKSUM(NEWID())) AS Value
           FROM       master..spt_values
           WHERE      type = 'p'
                      AND number BETWEEN 1 AND 1000
), cteRanking(Value, rnk, cnt, ntil)
AS (
           SELECT     Value,
                      DENSE_RANK() OVER (ORDER BY Value),
                      COUNT(*) OVER (),
                      NTILE(10) over (order by Value) as ntil
           FROM       cteRandomData
), ctePercentile(Value, Percentile, ntil)
AS (
           SELECT     Value,
                      100 * (rnk - 1) / cnt + 1 AS Percentile,
                      ntil
           FROM       cteRanking
)
SELECT     *
FROM       ctePercentile
--WHERE      Percentile = 10
```

 [1]: /index.php/DataMgmt/DBProgramming/come-one-come-all-to
 [2]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/olap_1.gif "T-SQL Tuesday logo"
 [3]: http://msdn.microsoft.com/en-us/library/ms173825.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms176102.aspx
 [5]: http://msdn.microsoft.com/en-us/library/ms186734.aspx
 [6]: https://connect.microsoft.com/SQLServer/feedback/details/124954/true-percentile-function-requested
 [7]: http://www.sqlmag.com/article/tsql3/calculate-percentiles.aspx
 [8]: http://tek-tips.com/viewthread.cfm?qid=1629693&page=1
 [9]: http://msdn.microsoft.com/en-us/library/ms175126.aspx