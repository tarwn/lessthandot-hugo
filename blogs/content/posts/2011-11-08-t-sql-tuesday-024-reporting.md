---
title: 'T-SQL Tuesday #024: Reporting Services, Stored Procedures, and Multiple Result Sets'
author: Jes Borland
type: post
date: 2011-11-08T16:44:00+00:00
ID: 1375
excerpt: |
  This month's T-SQL Tuesday topic: "Prox n' Funx".
url: /index.php/datamgmt/dbprogramming/t-sql-tuesday-024-reporting/
views:
  - 34276
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - SSRS

---
[![][1]][2]

It's TSQL Tuesday #024! It's been two years and the monthly blog party is going strong. Thanks to Brad Schulz for hosting this month! 

This month's topic is "Prox n' Funx". I am going to show something that frustrates me, and, from what I see on forums, a lot of other people. When building a dataset in Reporting Services that calls a stored procedure, if the stored procedure has multiple result sets, only the first is returned. 

I'm using SQL Server Reporting Services 2008R2 and AdventureWorks2008R2. I built a stored procedure that returns all products in a category, then a count of the number of products in the category, and the count of the number of products in that category in a specific color. 

```sql
ALTER PROCEDURE [dbo].[ProductCountByCatColor]
	@Category varchar(25), 
	@Color varchar(25)
AS
BEGIN
	SET NOCOUNT ON;

	SELECT PC.Name AS Category, 
		PROD.ProductNumber, 
		PROD.Name, 
		ISNULL(PROD.Color, 'N/A') AS Color, 
		ISNULL(PROD.Size, 'N/A') AS Size, 
		ISNULL(PROD.[Weight], 0) AS Weight 
	FROM Production.Product PROD 
		INNER JOIN Production.ProductSubcategory PS ON PS.ProductSubcategoryID = PROD.ProductSubcategoryID 
		INNER JOIN Production.ProductCategory PC ON PC.ProductCategoryID = PS.ProductCategoryID 
	WHERE PC.Name IN (@Category)
	ORDER BY Category 

	SELECT PC.Name AS Category, 
		COUNT(PROD.ProductNumber) as ProdCount 
	FROM Production.Product PROD 
		INNER JOIN Production.ProductSubcategory PS ON PS.ProductSubcategoryID = PROD.ProductSubcategoryID 
		INNER JOIN Production.ProductCategory PC ON PC.ProductCategoryID = PS.ProductCategoryID 
	WHERE PC.Name IN (@Category) 
	GROUP BY PC.Name
	ORDER BY PC.Name 

	SELECT PC.Name AS Category, 
		COUNT(PROD.ProductNumber) as ProdCount 
	FROM Production.Product PROD 
		INNER JOIN Production.ProductSubcategory PS ON PS.ProductSubcategoryID = PROD.ProductSubcategoryID 
		INNER JOIN Production.ProductCategory PC ON PC.ProductCategoryID = PS.ProductCategoryID 
	WHERE PC.Name IN (@Category) 
		AND ISNULL(PROD.Color, 'N/A') IN (@Color)
	GROUP BY PC.Name
	ORDER BY PC.Name 
END
```

When I execute the SP in SSMS, I get three result sets.
  
![][3]

Now, I set up my report to use a stored procedure in the dataset.
  
![][4]

I don't even need to run the query to notice that the dataset only shows fields from the first result set.
  
![][5]

When I run the report, I only see one set of results – the first listed in my stored procedure.
  
![][6]

**Why?** The short answer is: it's designed that way. According to BOL, "Multiple results sets from a single query are not supported." (<http://msdn.microsoft.com/en-us/library/dd239379.aspx>) 

How can you work around this? 

If each result set is a separate query, you can put each query in a separate stored procedure, or dataset. 

If the result sets need to tie together, you'll need to work with the T-SQL to make it one result set. That may require temp tables, UNIONs, or another solution. 

That's my short-and-sweet contribution to #tsql2sday24. Thanks for hosting, Brad!

 [1]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/olap_1.gif ""
 [2]: http://bradsruminations.blogspot.com/2011/10/invitation-for-t-sql-tuesday-024-prox-n.html
 [3]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/TSQL24ssmsquery.JPG?mtime=1320777536 ""
 [4]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/TSQL24dataset.JPG?mtime=1320777536 ""
 [5]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/TSQL24fields.JPG?mtime=1320777536 ""
 [6]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/TSQL24report.JPG?mtime=1320777537 ""