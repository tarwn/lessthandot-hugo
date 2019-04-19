---
title: Update Table Based on a JOIN
author: Ted Krueger (onpnt)
type: post
date: 2012-12-11T12:20:00+00:00
ID: 1849
excerpt: |
  This may seem like a simple and basic task but it is a common question asked, “How do I update a column based on a query that has joins in it?”
  Let's say you have a query such as the one below from AdventureWorks2012.
  SELECT per.LastName
  	,emp.JobTit&hellip;
url: /index.php/datamgmt/dbprogramming/update-table-based-on-a/
views:
  - 16356
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
<div class="image_block">
  <a href="http://sqlity.net/en/1175/t-sql-tuesday-37-invite-to-join-me-in-a-month-of-joins/"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/tsql2sday.gif?mtime=1355235468" width="98" height="99" align="left" /></a>
</div>

This month's T-SQL Tuesday is hosted by Sebastian Meine ([blog][1] | [twitter][2]) and the topic is joins. He has a whole month worth of topics about joins: [A Join A Day – Introduction][3].</p> 

I thought I'd write about a question I get often from Developers and some DBAs.
  
This may seem like a simple and basic task but it is a common question asked, “How do I update a column based on a query that has joins in it?”

Let's say you have a query such as the one below from AdventureWorks2012.

sql
SELECT per.LastName
	,emp.JobTitle
	,salesp.CommissionPct
	,terr.NAME
	,terr.SalesYTD
FROM [Person].[Person] per
INNER JOIN [HumanResources].[Employee] emp ON per.BusinessEntityID = emp.BusinessEntityID
INNER JOIN [Sales].[SalesPerson] salesp ON emp.BusinessEntityID = salesp.BusinessEntityID
INNER JOIN [Sales].[SalesTerritory] terr ON salesp.TerritoryID = terr.TerritoryID
WHERE terr.NAME = 'Northwest'
```

 

In the above query, let's say that you have to update CommissionPct by .002 for the territory NorthWest.  In order to do this, you need to join all the tables to satisfy the predicate of the territory name and ensure that only the sales people for that territory have the adjustment.

To perform this type of update, it truly is not much different than a regular update but in this case, you'll use the alias from the table that needs to be updated in the FROM.  You could really look at this as an UPDATE on a derived table.   Take a look at a typical update statement

sql
UPDATE [Sales].[SalesPerson] 
SET [Sales].[SalesPerson].CommissionPct = [Sales].[SalesPerson].CommissionPct + .002
```

 

In the update statement, the table SalesPerson is the object that is focused on or is being updated.  In this case, SalesPerson could reference an alias in order to qualify the path to the correct data to update.  So truly, you could perform this update like the below statement as well.

sql
UPDATE a
SET a.CommissionPct = a.CommissionPct + .002
FROM [Sales].[SalesPerson] a
```

 

Now that we know we can do this, why not use the FROM area to really pull together the information we need in order to make a successful update statement based on several bits of data from several tables that are joined together.

sql
UPDATE salesp
SET salesp.CommissionPct = salesp.CommissionPct + .002
FROM [Person].[Person] per
INNER JOIN [HumanResources].[Employee] emp ON per.BusinessEntityID = emp.BusinessEntityID
INNER JOIN [Sales].[SalesPerson] salesp ON emp.BusinessEntityID = salesp.BusinessEntityID
INNER JOIN [Sales].[SalesTerritory] terr ON salesp.TerritoryID = terr.TerritoryID
WHERE terr.NAME = 'Northwest'
```

 

Notice the use and qualifying of the alias salesp so we can find the direct path to the SalesPerson table and thus, CommissionPct that only applies to the Northwest Territory.  This statement would update all of the sales people that are in the Northwest sales territory based on the joining of the tables needed to gain the data path to the accurate rows to update.

**Summary**

This task may seem trivial and simplistic for many DBAs and Developers but we do have to remember that not everyone writes T-SQL daily or may have even written T-SQL in the past month.  It's the job of the DBA or Developer that has the knowledge of how to write accurate and functionally performing T-SQL code, for anyone that is in need or wants to learn the many methods in which we utilize this set-based language.

Happy T-SQLing!!!

 [1]: http://sqlity.net/en/
 [2]: https://twitter.com/sqlity
 [3]: http://sqlity.net/en/1146/a-join-a-day-introduction/