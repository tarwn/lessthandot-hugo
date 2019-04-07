---
title: Standardize Table Aliases
author: Sam Vanga
type: post
date: 2013-03-05T02:09:00+00:00
ID: 2023
excerpt: Inconsistent use of table aliases drives me crazy. Here is one way to standardize table aliases.
url: /index.php/datamgmt/dbprogramming/mssqlserver/standardize-table-aliases/
views:
  - 13654
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server

---
What&#8217;s wrong with the following code?

sql
SELECT 
    a.[BusinessEntityID]
    , b.[FirstName]
    , b.[LastName] 
FROM [HumanResources].[Employee] a
	INNER JOIN [Person].[Person] b
	ON b.[BusinessEntityID] = a.[BusinessEntityID]

```
Nothing &#8211; except for my poor choice of using meaningless single characters as table aliases. Although it&#8217;s not a big deal with simpler queries like I&#8217;ve here, it can be a maintenance nightmare with complex queries that join multiple tables.

What about now? Is there anything wrong still?

sql
SELECT 
    e.[BusinessEntityID]
    , p.[FirstName]
    , p.[LastName] 
FROM [HumanResources].[Employee] e
	INNER JOIN [Person].[Person] p
	ON e.[BusinessEntityID] = p.[BusinessEntityID]
```
No. This time I use e and p as aliases for Employee and Person respectively. Smart choice!

But I notice a problem in team environments. Different developers use different aliases for the same table resulting in confusion and inconsistency.

For example, some other developer might choose emp and ps instead of e and p like below.

sql
SELECT 
    emp.[BusinessEntityID]
    , ps.[FirstName]
    , ps.[LastName] 
FROM [HumanResources].[Employee] emp
	INNER JOIN [Person].[Person] ps
	ON emp.[BusinessEntityID] = ps.[BusinessEntityID]

```
<span class="MT_under">Solution:</span>

I use extended properties &#8211; following is an example script.

sql
EXEC sys.sp_addextendedproperty
@name = N'TableAlias', 
@value = N'emp', 
@level0type = N'SCHEMA', @level0name = HumanResources, 
@level1type = N'TABLE',  @level1name = Employee ;
GO

EXEC sys.sp_addextendedproperty 
@name = N'TableAlias', 
@value = N'per', 
@level0type = N'SCHEMA', @level0name = Person, 
@level1type = N'TABLE',  @level1name = Person ;
GO
```
Make no mistake, developers are still free to use different aliases, but it is at least easy to quickly see the standard alias by executing either of the following queries.

sql
SELECT [Schema] = s.NAME
	, [Table] = t.NAME
	, [Alias] = ep.value
FROM sys.tables t
INNER JOIN sys.schemas s ON s.schema_id = t.schema_id
LEFT OUTER JOIN sys.extended_properties ep ON ep.major_id = t.object_id
	AND ep.NAME = 'TableAlias' ;

SELECT *
FROM fn_listextendedproperty('TableAlias', 'schema', 'Person', 'table', 'Address', NULL, NULL)
```
Now I&#8217;ve to give a shout out to RedGate&#8217;s SQL Promt. In addition to other features, SQL Prompt allows you to automatically assign table aliases, and [specify custom aliases][1] **forcing you to use standard aliases**.

 [1]: http://www.red-gate.com/supportcenter/content/SQL_Prompt/help/5.3/SPT_Aliases