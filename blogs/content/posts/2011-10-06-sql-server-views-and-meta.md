---
title: SQL Server Views and Metadata
author: Ted Krueger (onpnt)
type: post
date: 2011-10-06T13:05:00+00:00
ID: 1341
excerpt: 'Something that came out of a recent session I gave at SQL Saturday in Iowa was a discussion on views and the Meta Data that comes along with them.  The discussion came about when I had commented, during a session that views were a pain spot for me.  Mis&hellip;'
url: /index.php/datamgmt/dbprogramming/sql-server-views-and-meta/
views:
  - 10371
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Something that came out of a recent session I gave at SQL Saturday in Iowa was a discussion on views and the Meta Data that comes along with them. The discussion came about when I had commented, during a session that views were a pain spot for me. Misuse by means of over using them as well as seeing over the years, very little attention to the Meta Data of them. I wanted to give a little more information on the discussion here.

Non-schema Bound Views in SQL Server rely on returning accurate and reliable results based from the underlying metadata. This is mostly in part to dependencies. With Schema-Bound Views this isn't as much of a concern since underlying objects cannot be altered without errors being generated. This is the definition of binding them together. With Non-schema Bound Views however, this isn't the case. In fact, we can create views without the underlying objects ever being in existence in some cases. Before we go into views and maintaining them, let's dig into the underlying object references we just mentioned.

**How to look at your dependencies?**

In order to review dependencies in SQL Server 2008+, we can use sys.sql\_expression\_dependencies. This Catalog View returns each dependency on an object created by a user. From BOL, this falls under the following listing

  * Schema-bound entities.
  * Non-schema-bound entities. 
  * Cross-database and cross-server entities. Entity names are reported; however, entity IDs are not resolved.
  * Column-level dependencies on schema-bound entities. 
  * Column-level dependencies for non-schema-bound objects can be returned by using sys.dm\_sql\_referenced_entities
  * Server-level DDL triggers when in the context of the master database.

Reference: http://msdn.microsoft.com/en-us/library/bb677315.aspx

Of course seeing this means much more. Let's take a look at AdventureWorks2008. The view HumanResources.vEmployee as the following definition

sql
SELECT 
e.[EmployeeID],c.[Title],c.[FirstName],c.[MiddleName],c.[LastName]
,c.[Suffix],e.[Title] AS [JobTitle] ,c.[Phone],c.[EmailAddress]
,c.[EmailPromotion],a.[AddressLine1],a.[AddressLine2],a.[City]
,sp.[Name] AS [StateProvinceName] ,a.[PostalCode],cr.[Name] AS [CountryRegionName] 
,c.[AdditionalContactInfo]
FROM [HumanResources].[Employee] e
INNER JOIN [Person].[Contact] c ON c.[ContactID] = e.[ContactID]
INNER JOIN [HumanResources].[EmployeeAddress] ea ON e.[EmployeeID] = ea.[EmployeeID] 
INNER JOIN [Person].[Address] a ON ea.[AddressID] = a.[AddressID]
INNER JOIN [Person].[StateProvince] sp ON sp.[StateProvinceID] = a.[StateProvinceID]
INNER JOIN [Person].[CountryRegion] cr ON cr.[CountryRegionCode] = sp.[CountryRegionCode];
```

Looking at this view's definition we can see that several columns are referred to from the tables. SQL Server tracks these dependencies between the objects by name. 

For example, take the view vEmployee. As the definition shows, the tables Employee, Contact, EmployeeAddress, Address, StateProvince and CountryRegion. Using sys.sql\_expression\_dependencies this can also be reviewed with a short query.

sql
SELECT 
	OBJECT_NAME(referencing_id) AS referencing_entity_name 
    ,referenced_server_name AS server_name
    ,referenced_database_name AS database_name
    ,referenced_schema_name AS schema_name
    ,referenced_entity_name
FROM sys.sql_expression_dependencies 
Where OBJECT_NAME(referencing_id) = 'vEmployee'
```

Resulting in 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/viewmeta_1.gif?mtime=1317908836"><img alt="" src="/wp-content/uploads/blogs/All/viewmeta_1.gif?mtime=1317908836" width="413" height="165" /></a>
</div>

The same tables listed in the referenceing\_id and using the OBJECT\_NAME to return the objects name for more meaningful information. Reversing this, in a sense, and adding the sys.objects catalog view, we can start by looking deeper into the dependencies and focus on one of the tables the view is referencing.

sql
SELECT 
	depends.referenced_entity_name,
    OBJECT_NAME(depends.referencing_id) AS referencing_entity_name, 
    objs.type_desc AS [Object Type]
FROM sys.sql_expression_dependencies AS depends
INNER JOIN sys.objects AS objs ON depends.referencing_id = objs.object_id
WHERE 
referenced_id = OBJECT_ID(N'HumanResources.Employee') 
And OBJECT_NAME(referencing_id) = N'vEmployee'; 
```
<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/-24.png?mtime=1317908836"><img alt="" src="/wp-content/uploads/blogs/All/-24.png?mtime=1317908836" width="401" height="74" /></a>
</div>

We now see that relationship in the results between the table and the view in a reverse reference based on the same dependency view. This can be taken a bit further in looking to the columns that are only EmployeeID.

sql
SELECT 
g.referenced_schema_name,
g.referenced_entity_name,
g.referenced_minor_name,
OBJECT_NAME(a.referencing_id) View_Name,
g.referenced_minor_id
FROM sys.sql_expression_dependencies a
CROSS APPLY sys.dm_sql_referenced_entities(OBJECT_SCHEMA_NAME(referencing_id) + '.' + OBJECT_NAME(referencing_id), 'OBJECT') g
WHERE OBJECT_NAME(a.referencing_id) = N'vEmployee'
AND g.referenced_minor_id > 0
AND referenced_minor_name = 'EmployeeID'
group by g.referenced_schema_name,
g.referenced_entity_name,
g.referenced_minor_name,
OBJECT_NAME(a.referencing_id),
g.referenced_minor_id
ORDER BY g.referenced_minor_id 
```

The results of this query can also be quickly obtained by using sys.objects, sys.schemas and the same sys.dm\_sql\_referenced\_entities. All of these catalog views expose the metadata that has been stored on this view. The above shows the referenced\_minor_id a numbering system in the view and relationship to the tables. This is where we get into trouble.
  
Take this short and the most common problem with this metadata and dependency issue. Run the statements as they are, in order.

sql
CREATE TABLE tbl (col INT, txt varchar(5))
GO

CREATE VIEW dbo.vTbl
AS
SELECT * FROM tbl
GO

SELECT * FROM sysdepends where id = OBJECT_ID('dbo.vTbl')

DROP TABLE tbl
GO

CREATE TABLE tbl (txt varchar(5), col INT)
GO
INSERT INTO tbl VALUES ('test2',2)
GO

SELECT * FROM vTbl
```
The results would be expected to show 2, test2 but they show the opposite, test2, 2. We will talk about the query in the middle of this batch looking at the results of sysdepends in a minute. 

So we can see the INT value is in the place holders of the varchar value. Imagine this with calculations being run on this view. The view is replying on the metadata already created basedoff the original ordinal order of the columns in tbl. This exact example is only possible with the use of the wildcard * but other metadata oddities can happen do to the naming resolutions.

Look at the table and view just created with the queries from earlier and verify the dependencies.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/-25.png?mtime=1317908836"><img alt="" src="/wp-content/uploads/blogs/All/-25.png?mtime=1317908836" width="452" height="62" /></a>
</div>

Nothing is jumping out until we look at the system view sysdepends and the results that were shown from the batch earlier. Those results are shown below

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/-26.png?mtime=1317908836"><img alt="" src="/wp-content/uploads/blogs/All/-26.png?mtime=1317908836" width="624" height="82" /></a>
</div>

Now, run the sysdepends check again
  
e.g. SELECT * FROM sysdepends where id = OBJECT_ID('dbo.vTbl')

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/-27.png?mtime=1317908836"><img alt="" src="/wp-content/uploads/blogs/All/-27.png?mtime=1317908836" width="589" height="156" /></a>
</div>

WHOA!!! Yes, we have a problem here and actually one of the points where the problem starts with what we tested above. Now we've identified this one reaction to the underlying table changes but there are more. Several issues can come from a non-schema bound view and the changes to underlying tables. Mostly the wildcard usage is the worst case practice. Like we said earlier, image calculations being run off the wrong column when the view is using an underlying definition that has been set to calculate things on specific columns. We could fall into sales reporting incorrectly, shipments going to the wrong locations and a mess of other possibilities.

**How to protect from this?**

In the case where views are used heavily, or even at all, coupled by developers or DBAs having the ability to change and alter anything on the database, get in the habit of refreshing the metadata for views weekly (even nightly if heavy usage is on your database). To do this we can use sp\_refreshview. This procedure will force a validation and update to the persistent metadata of the view. Sp\_refreshview takes a parameter of the view name to update. 

Run the following sp_refreshview and recheck sysdepends

sql
sp_refreshview 'dbo.vTbl'
GO
```

The results show that sysdepends now map correctly again.

To make this more usable in a large system or where many views are managed, a script like below can be used.

sql
DECLARE @viewname NVARCHAR(255)
DECLARE @looper INT = 1
IF OBJECT_ID('tempdb..#viewnames') IS NOT NULL
BEGIN
DROP TABLE #viewnames
END
SELECT 
s.[name] + '.' + v.[name] vname, 
ID = ROW_NUMBER() OVER (PARTITION BY v.[type_desc] ORDER BY v.[name])
INTO #viewnames
FROM sys.views v
JOIN sys.schemas s ON v.schema_id = s.schema_id
WHERE OBJECTPROPERTY(OBJECT_ID, 'IsSchemaBound') = 0
WHILE @looper <= (SELECT COUNT(*) FROM #viewnames)
BEGIN
SET @viewname = (SELECT vname FROM #viewnames WHERE ID = @looper)
EXEC SP_REFRESHVIEW @viewname
PRINT 'Exec sp_refreshview ''' + @viewname + ''''
SET @looper += 1
END
```

Reference: http://wiki.ltd.local/index.php/Sp\_refreshview\_for\_all\_views\_in\_a_database

**Closing**

Don't forget the views! They can be overused and become an extreme pain point for a DBA but they can be managed. Now if the view usage becomes to the point you have developers nesting view after view, rethink the designs and how you are obtaining your data.