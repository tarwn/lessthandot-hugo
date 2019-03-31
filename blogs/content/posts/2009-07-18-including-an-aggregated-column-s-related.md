---
title: Including an Aggregated Column’s Related Values
author: Erik
type: post
date: 2009-07-18T04:51:05+00:00
excerpt: "In this co-authored blog post, Naomi and I will present six different solutions to the commonly experienced query problem of how to include an aggregated column's related values - values from the same row in the group that provided the displayed value.&hellip;"
url: /index.php/datamgmt/dbprogramming/mssqlserver/including-an-aggregated-column-s-related/
views:
  - 31430
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server

---
In this co-authored blog post, [Naomi][1] and I will present six different solutions to the commonly experienced query problem of how to include an aggregated column&#8217;s related values &#8211; values from the same row in the group that provided the displayed value.

## Background

It&#8217;s a fairly simple query to select the maximum date for each group in a GROUP BY query. You just throw in a Max() on the date column and then GROUP BY the other columns. For example,

<pre>SELECT
   CustomerID,
   LastOrderDate = Max(OrderDate)
FROM Orders
GROUP BY CustomerID</pre>

But a common query need is to include other column values from the same row as the most recent date. Unfortunately, putting aggregates on other columns doesn&#8217;t work:

<pre>SELECT
   CustomerID,
   LastOrderDate = Max(OrderDate),
   LastSubTotal = Max(SubTotal) -- wrong: won't be the subtotal from Max(OrderDate), but the greatest order total ever placed by this customer.
FROM Orders
GROUP BY CustomerID</pre>

It&#8217;s almost like you need some kind of Max(OrderDate->SubTotal).

Note that the desired results are fairly easy in MS Access using the aggregate functions Last and First:

<pre>SELECT
   CustomerID,
   LastOrderDate = Last(OrderDate),
   SubTotal = Last(SubTotal)
FROM Orders
GROUP BY CustomerID
ORDER BY OrderDate</pre>

What happens in Access is that the Last() aggregate combines with the ORDER BY clause to select values from the row containing the most recent Order Date.

However, these functions are not available in SQL Server, likely because the query engine only does ordering at the very end, after GROUP BY and HAVING are processed. While this may seem like a defect, it is really a deliberate design trade-off, with compensations elsewhere in the engine.

## Test Case

In order to have enough data to make results significant and also to let others run the same queries against the same data, we are going to use the AdventureWorks sample database.

In our scenario, we want to show customers&#8217; account numbers along with the date and subtotal of their most recent order. We&#8217;re keeping it simple for clarity, but note that you can include as many columns as you like. In a later installment we&#8217;ll do these same queries with more columns and different scenarios, and will expand on some of the queries a bit. We&#8217;ll also discuss performance to help you select between them.

Note that the aggregate doesn&#8217;t have to be on a date. Perhaps you want to know the date of the largest dollar value order per customer. In this case, you&#8217;d be doing a Max() on the order value instead of a date, and you would most certainly run into duplicates. Keep this in mind when reading below and when developing your own queries.

The basic query we&#8217;ll be working with is:

<pre>SELECT
   C.AccountNumber,
   LastOrderDate = Max(O.OrderDate)
FROM
   Sales.Customer C
   INNER JOIN Sales.SalesOrderHeader O ON C.CustomerID = O.CustomerID</pre>

What we want to do is also return the Subtotal value from the same row as the LastOrderDate.

### Some Approaches to the Problem

We know of only a few basic approaches to the problem:

<div>
  &#8211; <strong>Key Search</strong> &#8212; use the LastOrderDate column as a key (along with customer AccountNumber) to locate the correct rows in the OrderHeader table. This requires an extra join and also the key isn&#8217;t unique, so we might have to perform another Max() on a unique column and join another time, with the new column added to the key.</p> 
  
  <p>
    &#8211; <strong>Number and Filter</strong> &#8212; select all rows from OrderHeader, and number them in a way exploitable to correctly filter out unwanted rows. Observe that this won&#8217;t require hitting the table again, but it could use a lot of extra resources to temporarily materialize more data. Skipping over obvious poor choices such as a temp table with an ordered update or a cursor, options might be a windowing function (SQL 2005 and up) or a temp table with an identity column (SQL 2000).
  </p>
  
  <p>
    &#8211; <strong>Simulate MS Access</strong> &#8212; simulate how MS Access works somehow, getting only the data we need in a single table access (seek or scan). Let&#8217;s imagine how the SQL engine already performs a Max. As it touches each row, it must store the intermediate value that is the best candidate so far for all the seen values. And it has to have one of these per group, per column. All that is needed is that every time it writes the Max() candidate into its temporary storage, it also writes the extra column we want into the same data.
  </p>
  
  <p>
    How could we do this ourselves? We need to ORDER BY OrderDate but actually yield SubTotal. SubTotal clearly won&#8217;t sort in the correct order by itself. Remembering Max(OrderDate->SubTotal), what if it wasn&#8217;t by itself, and somehow contained the OrderDate, too, to make it sort properly? So here we have an idea about packing both columns into a single value that sorts correctly yet can still be used to get the SubTotal back out. When the engine does its Max() bit as usual on the date and stores the best candidate, it will also be forced to store the other value at the same time (which we can extract later).
  </p>
</div>

So let&#8217;s use those ideas and turn them into real queries. Unless otherwise stated, queries will work with SQL Server 2000.

## Key Search</p> 

### 1. Correlated Subquery

  * Relies on unique order dates to avoid selecting multiple rows per customer.
  * Correlated subqueries perform well with small datasets and badly with large datasets (though better with SQL 2005 and 2008 because they can sometimes get converted to real joins).
  * Performance degrades geometrically as rows increase.
  * Does not handle NULLs correctly.

<pre>SELECT
   C.AccountNumber,
   O.OrderDate,
   O.SubTotal
FROM
   Sales.Customer C
   INNER JOIN Sales.SalesOrderHeader O ON C.CustomerID = O.CustomerID 
WHERE
   O.OrderDate = (
      SELECT Max(OrderDate)
      FROM SalesOrderHeader O2
      WHERE O2.CustomerID = O.CustomerID
   )</pre>

Another version of correlated subquery which sometimes performs much better is

<pre>SELECT
   C.AccountNumber,
   O.OrderDate,
   O.SubTotal
FROM
   Sales.Customer C
   INNER JOIN Sales.SalesOrderHeader O ON C.CustomerID = O.CustomerID 
WHERE
   O.OrderID = (
      SELECT top 1 OrderID 
      FROM SalesOrderHeader O2
      WHERE O2.CustomerID = O.CustomerID order by O2.OrderDate Desc, O2.OrderID DESC)
   </pre>

Another variation of this query is (suggested by Alejandro Mesa (Hunchback) in this [MSDN thread][2]):

<pre>SELECT
   C.AccountNumber,
   O1.OrderDate,
   O1.SubTotal
FROM
   Sales.Customer C
   INNER JOIN Sales.SalesOrderHeader O1 ON C.CustomerID = O1.CustomerID 
WHERE not exists (select 1 from Sales.SalesOrderHeader O2 where O2.CustomerID = O1.CustomerID and O1.OrderDate &lt; O2.OrderDate)</pre>

Note, that the first variation of this query may return duplicate rows, but the second will always return only one row per group (with the latest OrderID).

The third variation of this query with NOT EXISTS will produce duplicates in case of the same date, but can also be easily adjusted to return only one row with the max OrderID:

<pre>SELECT
   C.AccountNumber,
   O1.OrderDate,
   O1.SubTotal
FROM
   Sales.Customer C
   INNER JOIN Sales.SalesOrderHeader O1 ON C.CustomerID = O1.CustomerID 
WHERE not exists (select 1 from Sales.SalesOrderHeader O2 where O2.CustomerID = O1.CustomerID and (O1.OrderDate &lt; O2.OrderDate OR 
(O1.OrderDate = O2.OrderDate and O1.OrderID &lt; O2.OrderID)))</pre></p> 

### 2. Derived Table</p> 

  * Relies on unique order dates to avoid selecting multiple rows per customer.
  * Performs better than the correlated subquery when querying a significant portion of all customers, but worse when only querying a few, since the last order date for every customer is always calculated.
  * Performance degrades linearly as rows increase.
  * Does not handle NULLs correctly.

<pre>SELECT
   C.AccountNumber,
   O.OrderDate,
   O.SubTotal
FROM
   Sales.Customer C
   INNER JOIN Sales.SalesOrderHeader O ON C.CustomerID = O.CustomerID 
   INNER JOIN (
      SELECT CustomerID, LastOrderDate = Max(OrderDate)
      FROM Sales.SalesOrderHeader
      GROUP BY CustomerID
   ) O2 ON O.CustomerID = O2.CustomerID AND O.OrderDate = O2.LastOrderDate</pre>



## Number and Filter</p> 

### Windowing Functions

The windowing functions were introduced in SQL Server 2005, so the two queries below will not work in SQL Server 2000.

  * Will always return one row per customer. Using RANK() instead of ROW_NUMBER() will yield the same results as queries 1 and 2.
  * Easily supports not just 1 but _n_ rows per group.
  * Performance degrades fairly linearly as rows increase. The windowing functions are fast but huge rowsets can begin to bog them down.

### 3. Windowing Function &#8211; Two Stages</p> 

  * A common table expression is not needed, but is cleaner. The CTE query can be made into a derived table if desired.

<pre>WITH OrderData AS (
   SELECT
      C.AccountNumber,
      O.OrderDate,
      O.SubTotal,
      Selector = ROW_NUMBER() OVER (PARTITION BY O.CustomerID ORDER BY O.OrderDate DESC)
   FROM
      Sales.Customer C
      INNER JOIN Sales.SalesOrderHeader O ON C.CustomerID = O.CustomerID 
)
SELECT AccountNumber, OrderDate, SubTotal
FROM OrderData
WHERE Selector = 1</pre>

### 4. Windowing Function &#8211; One Stage

<pre>SELECT TOP 1 WITH TIES
   C.AccountNumber,
   O.OrderDate,
   O.SubTotal
FROM
   Sales.Customer C
   INNER JOIN Sales.SalesOrderHeader O ON C.CustomerID = O.CustomerID 
ORDER BY
   ROW_NUMBER() OVER (PARTITION by O.CustomerID ORDER BY OrderDate DESC)</pre>



## Simulate MS Access</p> 

### 5. Compound Key, aka Packed Values

  * Uses only one table access with a simple GROUP BY.
  * Naomi first learned this idea from [a Russian SQL FAQ][3].
  * Erik cooked up a similar idea in 2008.
  * This example uses a varchar column to hold the packed values, but be aware that converting to and from binary is faster than char (plus harder to use and more error-prone) and binary aggregation is faster. The crucial part is being able to faithfully extract the data that was put in.
  * This query has no problem with duplicates. It does effectively sort by the full compound value, so if multiple orders for the same customer can be on the same order date and you want some other column to determine which one is selected, it has to be included in the packed data.
  * As given, this query does not handle NULLs at all. A few strategic Coalesces can fix that.
  * There is no significant performance difference between unpacking one many-item compound value or many one-item compound values (though you&#8217;d always include all columns needed for ordering).

<pre>SELECT
   CustomerID,
   LastOrderDate,
   LastSubTotal = Convert(money, Substring(DateAndSubtotal, 20, 25))
FROM
   (
      SELECT
         CustomerID,
         LastOrderDate = Max(OrderDate),
         DateAndSubtotal = Max(convert(varchar(50), OrderDate, 121) + Convert(varchar(50), Subtotal))
      FROM Sales.SalesOrderHeader
      GROUP BY CustomerID
   ) X</pre>

Isn&#8217;t that cool? I don&#8217;t advocate using it all the time, but when performance is bad and the trade-offs are worth it, do it.

In closing, thank you for visiting. We hope this is useful to you. You might like to see the next installment [Including an Aggregated Column&#8217;s Related Values &#8211; Part 2][4], discussing this issue in further depth.

Erik

P.S. I would like to thank [Naomi][1] for co-authoring this blog post with me. She did the heavy lifting of actually writing queries and testing their performance; I just fleshed out the explanations a bit.

 [1]: http://forum.ltd.local/memberlist.php?mode=viewprofile&u=314
 [2]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/0217ddf4-bcab-4f2b-a81c-70841753cb23
 [3]: http://forum.foxclub.ru/read.php?32,177183,177232#msg-177232
 [4]: /index.php/DataMgmt/DataDesign/including-an-aggregated-column-s-related-2