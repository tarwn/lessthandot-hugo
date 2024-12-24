---
title: "It's Hard To Be \"Average\": Mean, Median, and Mode in SQL Server"
author: Jes Borland
type: post
date: 2012-02-08T12:45:00+00:00
ID: 1516
excerpt: |
  Average Joe. An average day. An average salary. An average run. An average house. 
  
  You hear about "averages" a lot. But what does it really mean? And why is this important to a SQL Server professional?
url: /index.php/datamgmt/dbprogramming/it-s-hard-to-be/
views:
  - 59385
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - average
  - mean
  - median
  - mode
  - sql server

---
Average Joe. An average day. An average salary. An average run. An average house. 

You hear about "averages" a lot. But what does it really mean? And why is this important to a SQL Server professional? 

**Being Average** 

An average is the "middle" value in a set of data values. When talking about or calculating an average, most people consider using the "mean". However, there are also "median" and "mode" to take into consideration. Each is calculated differently, and can produce drastically different results across a data set. As a data professional, particularly when working with business intelligence and reporting, knowing what data point the business or user needs to know is of utmost importance. 

Let's use the AdventureWorks2008R2 sample database to explore these functions. We'll use the SalesOrderHeader and SalesOrderDetail tables to determine "averages". 

To begin, create a view that takes the orders, by salesperson, for a year, and sums the number of orders and value of orders. 

```sql
CREATE VIEW Sales.OrdersBySalesperson 
AS 
SELECT SOH.SalesOrderID, SOH.OrderDate, SOH.SalesPersonID, SOH.CustomerID, SUM(SOD.OrderQty) AS Qty, SUM(SOD.LineTotal) AS Value
FROM Sales.SalesOrderHeader AS SOH 
	INNER JOIN Sales.SalesOrderDetail AS SOD ON SOD.SalesOrderID = SOH.SalesOrderID
WHERE SOH.OrderDate >= '20080101' 
	AND SOH.OrderDate < '20090101'
	AND NOT(SalesPersonID IS NULL)
GROUP BY SOH.SalesOrderID, SOH.OrderDate, SOH.SalesPersonID, SOH.CustomerID;
```

**Mean**

The mean is calculated by adding all the values in a data set, then dividing by the number of values in the set. 

In SQL Server, this can easily be achieved by using the AVG function. (Note that NULL values are ignored by this function.) 

```sql
SELECT SalesPersonID, AVG(Value) AS MeanValue 
FROM Sales.OrdersBySalesperson AS OBSP 
WHERE SalesPersonID IN (274, 275, 277, 278, 279, 282)
GROUP BY SalesPersonID 
ORDER BY SalesPersonID;
```

Results: 

![SQL Results](https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/mmm%20mean.JPG?mtime=1328707316)

**Median** 

The median is calculated by arranging all values in the data set in order, then determining the middle number. If there are an even number of values, you'll add the two in the middle and calculate the mean. In SQL Server, this isn't as easy to achieve. However, with the addition of common table expressions (CTEs) and ranking functions, it has become easier. 

First, we create a CTE that will order the sales value. The ROW_NUMBER function ranks the orders by value, looking at each salesperson separately. The COUNT function will tell us how many orders the salesperson has. 

```sql
WITH OrdersBySP (SPID, Value, RowNum, CountOrders) AS  
(
	SELECT SalesPersonID, 
		Value, 
		ROW_NUMBER() OVER (PARTITION BY SalesPersonID ORDER BY Value), 
		COUNT(SalesOrderID) OVER (PARTITION BY SalesPersonID)
	FROM Sales.OrdersBySalesperson AS OBSP
	WHERE SalesPersonID IN (274, 275, 277, 278, 279, 282)
)

SELECT SPID, Value, RowNum, CountOrders
FROM OrdersBySP;
```

Here's a sample of the results. As you can see, salesperson 275 has a total of 86 orders. Salesperson 277 has 97. 

![SQL Results](https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/mmm%20median%201.JPG?mtime=1328708140)

We're going to add a WHERE clause to the CTE: 

```sql
WITH OrdersBySP (SPID, Value, RowNum, CountOrders) AS  
(
	SELECT SalesPersonID, 
		Value, 
		ROW_NUMBER() OVER (PARTITION BY SalesPersonID ORDER BY Value), 
		COUNT(SalesOrderID) OVER (PARTITION BY SalesPersonID)
	FROM Sales.OrdersBySalesperson AS OBSP 
	WHERE SalesPersonID IN (274, 275, 277, 278, 279, 282)
)

SELECT SPID, Value, RowNum, CountOrders 
FROM OrdersBySP
WHERE RowNum BETWEEN (CountOrders + 1)/2 AND (CountOrders + 2)/2;
```

![SQL Results](https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/mmm%20median%202.JPG?mtime=1328708437)

What does this _mean_? (Please laugh at that.) Remember, to calculate median, we have to determine the middle row. This will vary depending on whether we have an even or odd number of rows. So, for a salesperson with an even number of orders, it is getting the two in the middle. For a salesperson with an odd number of orders, it is getting the one in the middle. 

The next query will find the average of the two middle values, if necessary. If the two middle values are not the same, it will return a value that is not in the data set (and that's OK!). 

```sql
WITH OrdersBySP (SPID, Value, RowNum, CountOrders) AS  
(
	SELECT SalesPersonID, 
		Value, 
		ROW_NUMBER() OVER (PARTITION BY SalesPersonID ORDER BY Value), 
		COUNT(SalesOrderID) OVER (PARTITION BY SalesPersonID)
	FROM Sales.OrdersBySalesperson AS OBSP 
	WHERE SalesPersonID IN (274, 275, 277, 278, 279, 282)
)

SELECT SPID, AVG(Value) as AvgValue 
FROM OrdersBySP
WHERE RowNum BETWEEN (CountOrders + 1)/2 AND (CountOrders + 2)/2 
GROUP BY SPID;
```

![SQL Results](https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/mmm%20median%203.JPG?mtime=1328708609)

You can see that this yields a different result set than using the AVG function does. 

**Median in SQL Server 2012** 

In the newest version of SQL Server, it gets even easier to calculate a median value. There is a new function, PERCENTILE_CONT. It "calculates a percentile based on a continuous distribution of the column value" (to quote Books Online). Let's see how this works. 

You will specify the percentile you want – in this case, we want the 50th, so we use (0.5). Then, we use WITHIN GROUP (ORDER BY ... ) to tell the function which values to sort and compute. I'm using OVER (PARTITION BY ... ) to tell the function that I want to divide the values up by salesperson. 

```sql
SELECT DISTINCT OBSP.SalesPersonID, 
	PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY OBSP.Value) 
		OVER (PARTITION BY OBSP.SalesPersonID) AS MedianCont 
FROM Sales.OrdersBySalesperson AS OBSP
WHERE OBSP.SalesPersonID IN (274, 275, 277, 278, 279, 282)
ORDER BY OBSP.SalesPersonID;
```

![SQL Results](https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/mmm%20median%204.JPG?mtime=1328711541)

It will look for a value in the percentile you request. The value will not necessarily exist in the set. If you want a function that looks at the values in the set and returns one in it, use PERCENTILE_DISC. 

This is a fantastic improvement! 

**Mode** 

The mode is the most frequently occurring value in a data set. This is more useful with a data set that has a lot of values that are the same. 

To translate this into T-SQL, let's look at the quantities of an item sold. We'll get the COUNT of the quantity, and put it in descending order to see what quantity of the item is ordered most frequently. 

```sql
SELECT COUNT(SOD.OrderQty) AS FrequencyOfValue, SOD.OrderQty
FROM Sales.SalesOrderHeader AS SOH 
	INNER JOIN Sales.SalesOrderDetail AS SOD ON SOD.SalesOrderID = SOH.SalesOrderID
WHERE SOH.OrderDate >= '20080101' 
	AND SOH.OrderDate < '20090101' 
	AND NOT(SOH.SalesPersonID IS NULL) 
		AND SOD.ProductID IN (864) 
GROUP BY SOD.OrderQty
ORDER BY COUNT(SOD.OrderQty) DESC;
```

![SQL Results](https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/mmm%20mode%201.JPG?mtime=1328711838)

Now, we will use the TOP clause to get the most-frequently-ordered quantity. Make sure you use WITH TIES so that if if two or more values occur with the same frequency, both are listed. 

```sql
SELECT TOP 1 WITH TIES SOD.OrderQty
FROM Sales.SalesOrderHeader AS SOH 
	INNER JOIN Sales.SalesOrderDetail AS SOD ON SOD.SalesOrderID = SOH.SalesOrderID
WHERE SOH.OrderDate >= '20080101' 
	AND SOH.OrderDate < '20090101'  
	AND NOT(SOH.SalesPersonID IS NULL) 
		AND SOD.ProductID IN (864) 
GROUP BY SOD.OrderQty
ORDER BY COUNT(SOD.OrderQty) DESC;
```

![SQL Results](https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/MMM%20mode2.JPG?mtime=1328711969)

**It's Hard to Be Average** 

As you've seen, "average" can mean more than one thing. Make sure that you understand the differences between the three options, and which one your users need to see in a given case. 

**_Resources_**

LTD's own George Mastros [wrote about this in 2008](/index.php/DataMgmt/DataDesign/calculating-mean-median-and-mode-with-sq) 

Itzik Ben-Gan tackled median here: [Calculating the median gets simpler in SQL Server 2005](https://www.itprotoday.com/sql-server/calculating-median-gets-simpler-sql-server-2005).
