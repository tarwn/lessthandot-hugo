---
title: Aggregates with multiple tables
author: Naomi Nosonovsky
type: post
date: 2011-09-25T18:13:00+00:00
excerpt: 'In this blog post I would like to touch a very common problem which often trips up newbies and even serious SQL Professionals. I wanted to write this blog post for quite some time, but always put it aside. However, just a few days ago I have been dealin&hellip;'
url: /index.php/datamgmt/datadesign/aggregates-with-multiple-tables/
views:
  - 14091
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
In this blog post I would like to touch a very common problem which often trips up newbies and even serious SQL Professionals. I wanted to write this blog post for quite some time, but always put it aside. However, just a few days ago I have been dealing with this problem at my work place, so I believe it&#8217;s time to discuss the problem.

For the discussion let&#8217;s consider AdventureWorks database SalesOrderHeader and SalesOrderDetail tables. To illustrate the problem, let&#8217;s assume that returns are handled in a different table with a structure very similar to SalesOrderDetail. And let&#8217;s assume there are about 20 percent of returns (the business must not be doing too well!).

Ok, to create the returns table in the Sales schema let&#8217;s use the following code:

<pre>SELECT IDENTITY(INT) AS ReturnID, D.LineTotal AS ReturnTotal, D.ProductID, D.OrderQty, D.UnitPrice, D.SalesOrderID
INTO Sales.SalesReturns
FROM Sales.SalesOrderDetail D TABLESAMPLE (20 PERCENT)</pre>

Now, suppose we need to get quantity ordered and quantity returned as well as line total for both quantity and returned grouped by Customer ID. Let&#8217;s write the most intuitive query:

<pre>SELECT OH.CustomerID, 
       COALESCE(SUM(OD.OrderQty),0) AS [Ordered quantity],
       COALESCE(SUM(OD.LineTotal),0) AS [Ordered],
       COALESCE(SUM(R.OrderQty),0) AS [Returned quantity],       
       COALESCE(SUM(R.ReturnTotal),0) AS [Returned]

FROM Sales.SalesOrderHeader OH LEFT JOIN Sales.SalesOrderDetail OD ON OH.SalesOrderID = OD.SalesOrderID
LEFT JOIN Sales.SalesReturns R ON OH.SalesOrderID = R.SalesOrderID 
GROUP BY OH.CustomerID 
ORDER BY OH.CustomerID </pre>

Which gives us these results

<div class="tables">
  <center>
    </p> <table border="1" cellpadding ="5" cellspacing = "5"> <caption style="color:blue;font-weight:bold">Aggregate Query Results &#8211; Ordered and Returned</caption> 
    
    <tr>
      <th align="right">
        CustomerID
      </th>
      
      <th align="right">
        Ordered quantity
      </th>
      
      <th align="right">
        Ordered
      </th>
      
      <th align="right">
        Returned quantity
      </th>
      
      <th align="right">
        Returned
      </th>
    </tr>
    
    <tr>
      <td align="right">
        1
      </td>
      
      <td align="right">
        882
      </td>
      
      <td align="right">
        724610.142300
      </td>
      
      <td align="right">
        857
      </td>
      
      <td align="right">
        713619.368600
      </td>
    </tr>
    
    <tr>
      <td align="right">
        2
      </td>
      
      <td align="right">
        198
      </td>
      
      <td align="right">
        25199.499392
      </td>
      
      <td align="right">
      </td>
      
      <td align="right">
        0.000000
      </td>
    </tr>
    
    <tr>
      <td align="right">
        3
      </td>
      
      <td align="right">
        20140
      </td>
      
      <td align="right">
        4084947.217763
      </td>
      
      <td align="right">
        18916
      </td>
      
      <td align="right">
        3814775.599788
      </td>
    </tr>
    
    <tr>
      <td align="right">
        4
      </td>
      
      <td align="right">
        8268
      </td>
      
      <td align="right">
        4493242.544792
      </td>
      
      <td align="right">
        7530
      </td>
      
      <td align="right">
        4036924.558800
      </td>
    </tr>
    
    <tr>
      <td align="right">
        5
      </td>
      
      <td align="right">
        1190
      </td>
      
      <td align="right">
        325894.561736
      </td>
      
      <td align="right">
        973
      </td>
      
      <td align="right">
        261739.811536
      </td>
    </tr></table> 
    
    <p>
      </center> </div> 
      
      <p>
        Now, let&#8217;s verify these results by only running Ordered amounts.
      </p>
      
      <p>
        Let&#8217;s run this query (I re-wrote using CTE for simplicity):
      </p>
      
      <pre>;with cte as (SELECT OH.CustomerID, 
       COALESCE(SUM(OD.OrderQty),0) AS [Ordered quantity],
       COALESCE(SUM(OD.LineTotal),0) AS [Ordered]

FROM Sales.SalesOrderHeader OH LEFT JOIN Sales.SalesOrderDetail OD ON OH.SalesOrderID = OD.SalesOrderID
GROUP BY OH.CustomerID 
)
SELECT * FROM cte</pre>
      
      <p>
        which produces the following results:
      </p>
      
      <div class="tables">
        <center>
          </p> <table border="1" cellpadding ="5" cellspacing = "5"> <caption style="color:blue;font-weight:bold">Aggregate Query Results &#8211; Ordered only</caption> 
          
          <tr>
            <th align="right">
              CustomerID
            </th>
            
            <th align="right">
              Ordered quantity
            </th>
            
            <th align="right">
              Ordered
            </th>
          </tr>
          
          <tr>
            <td align="right">
              1
            </td>
            
            <td align="right">
              121
            </td>
            
            <td align="right">
              85177.081200
            </td>
          </tr>
          
          <tr>
            <td align="right">
              2
            </td>
            
            <td align="right">
              198
            </td>
            
            <td align="right">
              25199.499392
            </td>
          </tr>
          
          <tr>
            <td align="right">
              3
            </td>
            
            <td align="right">
              1695
            </td>
            
            <td align="right">
              361999.058170
            </td>
          </tr>
          
          <tr>
            <td align="right">
              4
            </td>
            
            <td align="right">
              980
            </td>
            
            <td align="right">
              586524.947384
            </td>
          </tr>
          
          <tr>
            <td align="right">
              5
            </td>
            
            <td align="right">
              299
            </td>
            
            <td align="right">
              86177.847024
            </td>
          </tr></table> 
          
          <p>
            </center> </div> 
            
            <p>
              We see that numbers differ dramatically. So, what did happen in our first query? Why did it produce such high numbers that have nothing to do with the real numbers?
            </p>
            
            <p>
              The reason is that for each row of Ordered results we get N Returned rows and so the total number of rows per one customer became M*N, where M is number of Ordered rows and N number of Returned rows. Therefore our results get much higher numbers.
            </p>
            
            <p>
              So, the rule I always employ when I need to do aggregates that will involve more than one table to JOIN is to produce the necessary results separately as derived tables or CTE and only then join them together to form final result.
            </p>
            
            <p>
              Here is how our query works when I re-write it using this idea:
            </p>
            
            <pre>;WITH Ordered AS (SELECT OH.CustomerID, 
       COALESCE(SUM(OD.OrderQty),0) AS [Ordered Quantity],
       COALESCE(SUM(OD.LineTotal),0) AS [Ordered]

FROM Sales.SalesOrderHeader OH LEFT JOIN Sales.SalesOrderDetail OD ON OH.SalesOrderID = OD.SalesOrderID
Group BY OH.CustomerID 
),

Returned AS (SELECT OH.CustomerID, 
       COALESCE(SUM(R.OrderQty),0) AS [Returned Quantity],
       COALESCE(SUM(R.ReturnTotal),0) AS [Returned]

FROM Sales.SalesOrderHeader OH LEFT JOIN Sales.SalesReturns R ON OH.SalesOrderID = R.SalesOrderID
Group BY OH.CustomerID 
)

SELECT COALESCE(O.CustomerID, R.CustomerID) AS CustomerID, 
COALESCE(O.[Ordered Quantity],0) AS [Ordered Quantity],
COALESCE(O.[Ordered],0) AS [Ordered], 
COALESCE(R.[Returned Quantity],0) AS [Returned Quantity],
COALESCE(R.[Returned],0) AS [Returned]

FROM Ordered O FULL JOIN Returned R ON O.CustomerID = R.CustomerID 
ORDER BY COALESCE(O.CustomerID, R.CustomerID)</pre>
            
            <p>
              with these correct now results:
            </p>
            
            <div class="tables">
              <center>
                </p> <table border="1" cellpadding ="5" cellspacing = "5"> <caption style="color:blue;font-weight:bold">Aggregate Query Results &#8211; Ordered and Returned</caption> 
                
                <tr>
                  <th align="right">
                    CustomerID
                  </th>
                  
                  <th align="right">
                    Ordered Quantity
                  </th>
                  
                  <th align="right">
                    Ordered
                  </th>
                  
                  <th align="right">
                    Returned Quantity
                  </th>
                  
                  <th align="right">
                    Returned
                  </th>
                </tr>
                
                <tr>
                  <td align="right">
                    1
                  </td>
                  
                  <td align="right">
                    121
                  </td>
                  
                  <td align="right">
                    85177.081200
                  </td>
                  
                  <td align="right">
                    44
                  </td>
                  
                  <td align="right">
                    37199.802700
                  </td>
                </tr>
                
                <tr>
                  <td align="right">
                    2
                  </td>
                  
                  <td align="right">
                    198
                  </td>
                  
                  <td align="right">
                    25199.499392
                  </td>
                  
                  <td align="right">
                  </td>
                  
                  <td align="right">
                    0.000000
                  </td>
                </tr>
                
                <tr>
                  <td align="right">
                    3
                  </td>
                  
                  <td align="right">
                    1695
                  </td>
                  
                  <td align="right">
                    361999.058170
                  </td>
                  
                  <td align="right">
                    471
                  </td>
                  
                  <td align="right">
                    91827.440195
                  </td>
                </tr>
                
                <tr>
                  <td align="right">
                    4
                  </td>
                  
                  <td align="right">
                    980
                  </td>
                  
                  <td align="right">
                    586524.947384
                  </td>
                  
                  <td align="right">
                    242
                  </td>
                  
                  <td align="right">
                    130206.961392
                  </td>
                </tr>
                
                <tr>
                  <td align="right">
                    5
                  </td>
                  
                  <td align="right">
                    299
                  </td>
                  
                  <td align="right">
                    86177.847024
                  </td>
                  
                  <td align="right">
                    82
                  </td>
                  
                  <td align="right">
                    22023.096824
                  </td>
                </tr></table> 
                
                <p>
                  </center> </div> 
                  
                  <p>
                    I used the FULL JOIN just in case we may have Ordered items and no returns or vs. versa for the customer.
                  </p>
                  
                  <p>
                    I received a question of how to write this code for SQL 2000. In SQL 2000 we can not use CTE (common table expressions), but we can use derived tables, so the code above will be re-written in this manner:
                  </p>
                  
                  <pre>SELECT COALESCE(O.CustomerID, R.CustomerID) AS CustomerID,
COALESCE(O.[Ordered Quantity],0) AS [Ordered Quantity],
COALESCE(O.[Ordered],0) AS [Ordered],
COALESCE(R.[Returned Quantity],0) AS [Returned Quantity],
COALESCE(R.[Returned],0) AS [Returned]
 
FROM (SELECT OH.CustomerID,
       COALESCE(SUM(OD.OrderQty),0) AS [Ordered Quantity],
       COALESCE(SUM(OD.LineTotal),0) AS [Ordered]
 
FROM Sales.SalesOrderHeader OH LEFT JOIN Sales.SalesOrderDetail OD ON OH.SalesOrderID = OD.SalesOrderID
GROUP BY OH.CustomerID
)O FULL JOIN (SELECT OH.CustomerID,
       COALESCE(SUM(R.OrderQty),0) AS [Returned Quantity],
       COALESCE(SUM(R.ReturnTotal),0) AS [Returned]
 
FROM Sales.SalesOrderHeader OH LEFT JOIN Sales.SalesReturns R ON OH.SalesOrderID = R.SalesOrderID
GROUP BY OH.CustomerID
) R ON O.CustomerID = R.CustomerID
ORDER BY COALESCE(O.CustomerID, R.CustomerID)</pre>
                  
                  <p>
                    So, hopefully you can now easily recognize the problem and apply the same idea when dealing with a similar situation.
                  </p>
                  
                  <p>
                    Say, few days ago I looked into report. The number of joins used in the query made my head dizzy &#8211; I told right away that because of the JOINs the total results are not going to be correct.
                  </p>
                  
                  <p>
                    So, how did I solve the problem? I included the totals using WINDOW AGGREGATE function in the CTE first and then added the rest of the JOINs to get the data needed for report.
                  </p>
                  
                  <p>
                    Unfortunately, I am not sure if an easier method exists for reports in SSRS when we need to present information for customer orders, for example, but also display customer addresses (and there may be many addresses per customer). Obviously, we will not get correct totals if we will just sum the records &#8211; we need to apply some tricks, simplest of them will be to pre-calculate totals and include these fields as part of the returned dataset. I will be interested to know your solutions for this problem.
                  </p>
                  
                  <p>
                    I should also mention another way to solve this query (assuming we also want to return the Customer name) by using OUTER APPLY query.
                  </p>
                  
                  <p>
                    Here I am showing two different approaches for comparison. The first approach performs better.
                  </p>
                  
                  <pre>;WITH Ordered AS (SELECT OH.CustomerID, 
       COALESCE(SUM(OD.OrderQty),0) AS [Ordered Quantity],
       COALESCE(SUM(OD.LineTotal),0) AS [Ordered]

FROM Sales.SalesOrderHeader OH LEFT JOIN Sales.SalesOrderDetail OD ON OH.SalesOrderID = OD.SalesOrderID
Group BY OH.CustomerID 
),

Returned AS (SELECT OH.CustomerID, 
       COALESCE(SUM(R.OrderQty),0) AS [Returned Quantity],
       COALESCE(SUM(R.ReturnTotal),0) AS [Returned]

FROM Sales.SalesOrderHeader OH LEFT JOIN Sales.SalesReturns R ON OH.SalesOrderID = R.SalesOrderID
Group BY OH.CustomerID 
)

SELECT TOP (10) C.FirstName, C.LastName, 
COALESCE(O.[Ordered Quantity],0) AS [Ordered Quantity],
COALESCE(O.[Ordered],0) AS [Ordered], 
COALESCE(R.[Returned Quantity],0) AS [Returned Quantity],
COALESCE(R.[Returned],0) AS [Returned]
--INTO dbo.QueryResults
FROM Sales.vIndividualCustomer C
LEFT JOIN Ordered O On C.CustomerID = O.CustomerID 
LEFT JOIN Returned R ON C.CustomerID = R.CustomerID 
ORDER BY C.CustomerID 


SELECT TOP (10) C.CustomerID, C.FirstName, C.LastName,  
COALESCE(O.[Ordered Quantity],0) AS [Ordered Quantity],
COALESCE(O.[Ordered],0) AS [Ordered], 
COALESCE(R.[Returned Quantity],0) AS [Returned Quantity],
COALESCE(R.[Returned],0) AS [Returned]
FROM Sales.vIndividualCustomer C
OUTER APPLY (SELECT SUM(OD.OrderQty) AS [Ordered Quantity],
SUM(OD.LineTotal) AS [Ordered]
FROM Sales.SalesOrderHeader OH 
LEFT JOIN Sales.SalesOrderDetail OD ON OH.SalesOrderID = OD.SalesOrderID
WHERE OH.CustomerID = C.CustomerID) O
 
OUTER APPLY (SELECT 
       COALESCE(SUM(R.OrderQty),0) AS [Returned Quantity],
       COALESCE(SUM(R.ReturnTotal),0) AS [Returned]

FROM Sales.SalesOrderHeader OH 
LEFT JOIN Sales.SalesReturns R ON OH.SalesOrderID = R.SalesOrderID
WHERE OH.CustomerID = C.CustomerID) R
ORDER BY C.CustomerID </pre>
                  
                  <p>
                    *** <strong>Remember, if you have a SQL related question, try our <a href="http://forum.lessthandot.com/viewforum.php?f=17">Microsoft SQL Server Programming</a> forum or our <a href="http://forum.lessthandot.com/viewforum.php?f=22">Microsoft SQL Server Admin</a> forum</strong><ins></ins>
                  </p>