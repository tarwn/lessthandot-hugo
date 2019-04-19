---
title: 'SQL Advent 2011 Day 23: OBJECT_DEFINITION'
author: SQLDenis
type: post
date: 2011-12-23T18:24:00+00:00
ID: 1460
excerpt: 'Back in the day if you wanted to see the body of a stored procedure you had to use the syscomments table and then concatenate rows because syscomments  only stored 4000 characters per row. You could also have used sp_helptext. SQL Server introduced the OBJECT_DEFINITION function.'
url: /index.php/datamgmt/datadesign/object_definition-sql-advent-2011-day/
views:
  - 14975
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - metadata
  - object_definition
  - sql server 2005
  - sql server 2008
  - stored procedures

---
In my [Are you ready for SQL Server 2012 or are you still partying like it is 1999?][1] post, I wrote about how you should start using SQL Server 2005 and SQL Server 2008 functionality now in order to prepare for SQL Server 2012. I still see tons of code that is written in the pre 2005 style and people still keep using those functions, procs and statements even though SQL Server 2005 and 2008 have much better functionality.

Today we are going to take a look at the OBJECT_DEFINITION function. I am using the AdventureWorks2008R2 for these examples but I think AdventureWorks should also work.

Back in the day if you wanted to see the body of a stored procedure you had to use the syscomments table and then concatenate rows because syscomments only stored 4000 characters per row. You could also have used sp\_helptext. SQL Server introduced the OBJECT\_DEFINITION function. The OBJECT_DEFINITION function returns nvarchar(max) and applies to the following object types

C = Check constraint

D = Default (constraint or stand-alone)

P = SQL stored procedure

FN = SQL scalar function

R = Rule

RF = Replication filter procedure

TR = SQL trigger (schema-scoped DML trigger, or DDL trigger at either the database or server scope)

IF = SQL inline table-valued function

TF = SQL table-valued function

V = View

So let's take a quick look

Here is how you would use sp_helptext 

```sql
EXEC sp_helptext 'uspGetBillOfMaterials'
```

That returns the definition of the stored procedure

Here is how to use the OBJECT_DEFINITION function

```sql
SELECT OBJECT_DEFINITION(OBJECT_ID('uspGetBillOfMaterials'))
```

That also returns the definition of the stored procedure

So you say...so what, what is the big deal, seems the same to me? I say, hold on, let me show you this......what if I wanted to have the definition of every trigger, stored procedure or function that references the Production.BillOfMaterials table. Here is how simple that is

```sql
SELECT OBJECT_DEFINITION(OBJECT_ID),OBJECT_NAME(OBJECT_ID) AS  NameOfObject
FROM sys.all_sql_modules a
JOIN sys.sysobjects  s ON a.object_id = s.id
AND xtype IN('TR','P','FN','IF','TF')
WHERE OBJECTPROPERTYEX(OBJECT_ID,'IsMSShipped') =0
AND REPLACE(REPLACE(OBJECT_DEFINITION(OBJECT_ID),']',''),'[','') like '%Production.BillOfMaterials%'
```

Notice that I am using a replace statement to filter out brackets

But there is an easier way, you don't even need the function in this case, the sys.all\_sql\_modules object catalog view has already a definition column

Here is how you do it

```sql
SELECT definition,OBJECT_NAME(OBJECT_ID) AS  NameOfObject
FROM sys.all_sql_modules a
JOIN sysobjects  s ON a.object_id = s.id
AND xtype IN('TR','P','FN','IF','TF')
WHERE OBJECTPROPERTYEX(OBJECT_ID,'IsMSShipped') =0
AND REPLACE(REPLACE(definition,']',''),'[','') like '%Production.BillOfMaterials%'
```

Create this simple proc

```sql
CREATE PROCEDURE prTest 
AS
SELECT * FROM Production.BillOfMaterials
GO
```
Run this query again

```sql
SELECT definition,OBJECT_NAME(OBJECT_ID) AS  NameOfObject
FROM sys.all_sql_modules a
JOIN sysobjects  s ON a.object_id = s.id
AND xtype IN('TR','P','FN','IF','TF')
WHERE OBJECTPROPERTYEX(OBJECT_ID,'IsMSShipped') =0
AND REPLACE(REPLACE(definition,']',''),'[','') like '%Production.BillOfMaterials%'
```

You should see one row more now compared to before.

Come back tomorrow for another post in this series

 [1]: /index.php/DataMgmt/DataDesign/are-you-ready-for-sql