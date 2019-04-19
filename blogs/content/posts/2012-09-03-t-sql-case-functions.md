---
title: T-SQL CASE functions
author: Axel Achten (axel8s)
type: post
date: 2012-09-03T10:12:00+00:00
ID: 1709
excerpt: 'With the CASE statement you can add conditional logic to your T-SQL code. T-SQL 2012 has certain functions that can be seen as CASE shortcuts. With these functions you can quickly use some CASE functionality and as a surplus, because they are functions&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/t-sql-case-functions/
views:
  - 27439
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - case
  - choose
  - coalesce
  - iif
  - isnull
  - sql server 2012
  - t-sql

---
With the CASE statement you can add conditional logic to your T-SQL code. T-SQL 2012 has certain functions that can be seen as CASE shortcuts. With these functions you can quickly use some CASE functionality and as a surplus, because they are functions you can use them everywhere where expressions are allowed. The examples are written against the AdventureWorks2012 database:

**ISNULL**

The ISNULL('Null\_Expression','Replace\_Value') function evaluates the value of the 'Null\_Expression' and if the result is NULL it returns the 'Replace\_Value' value. Otherwise the 'Null_Expression' value is returned:

sql
SELECT TOP 10 ISNULL(MiddleName,'NA')AS NoNullMiddleName FROM Person.Person
```

In the result we see that all the NULL values are replaced by the 'NA' value. Values that were not NULL keep their value:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/CaseSC1.gif?mtime=1346673979"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/CaseSC1.gif?mtime=1346673979" width="154" height="235" /></a>
</div>

**COALESCE**

This function accepts 2 or more arguments and will return the first non NULL value. In the next example I use a common table expression and fill it with items with a weight and no size, items with a size and no weight and items with a size and weight:

sql
WITH Product_CTE (name, size, weight) AS
(
	SELECT TOP 10 name, size, weight FROM Production.Product
	WHERE size IS NOT NULL AND weight IS NULL
	UNION
	SELECT TOP 10 name, size, weight FROM Production.Product
	WHERE weight IS NOT NULL AND size IS NULL
	UNION
	SELECT TOP 10 name, size, weight FROM Production.Product
	WHERE weight IS NOT NULL AND size IS NOT NULL
)
Select name, size, weight, COALESCE(size,CAST(weight AS VARCHAR(10))) AS coalesceresult
 FROM Product_CTE
```

In the result you see that the size is shown in the coalesceresult column unless the original value was NULL. In that case the weight value is returned:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/CaseSC2.gif?mtime=1346673994"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/CaseSC2.gif?mtime=1346673994" width="369" height="269" /></a>
</div>

**IIF**

Just like in SSIS and MS Access, SQL Server 2012 now supports the IIF('logical\_expression','expression\_if\_true','expression\_if\_false') function. So if the 'logical\_expression evaluates to TRUE the 'expression\_if\_true' value is shown, otherwise the 'expression\_if\_false' is executed:

sql
SELECT IIF(Title LiKE '%s.','Miss','Mister') AS Title, FirstName 
 FROM Person.Person
 WHERE Title IS NOT NULL
```

Since IIF supports the true/false expressions you can even put logical expressions in their place to create single line CASE scenarios to have more possibilities. For ease of use and general readability of your code, I would not recommend this. The result of the previous query:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/CaseSC3.gif?mtime=1346674010"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/CaseSC3.gif?mtime=1346674010" width="154" height="257" /></a>
</div>

**CHOOSE**

CHOOSE('index','expr1','expr2',...,'exprn') is also known in MS Access and new in SQL Server 2012 and gives the possibility to use an index number and the result of the corresponding index expression:

sql
SELECT FirstName, LastName, MiddleName, CHOOSE(2,FirstName, LastName, MiddleName) AS Chosen
 FROM Person.Person
```

In normal coding you would dynamically assign a value to the index with a variable. The result of the above query is:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/CaseSC4.gif?mtime=1346674021"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/CaseSC4.gif?mtime=1346674021" width="330" height="256" /></a>
</div>

**Conclusion**

The CASE shortcuts can be very useful in coding scenarios but note that only COALESCE is ANSI standard. IIF and CHOOSE are added to the T-SQL 2012 language to ease the migration from MS Access to SQL Server.