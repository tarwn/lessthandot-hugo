---
title: Maximum length of data in every column in a table
author: Naomi Nosonovsky
type: post
date: 2011-12-06T13:41:00+00:00
ID: 1420
excerpt: |
  In this short blog post I would like to share a script I wrote yesterday as an answer to this MSDN thread to find maximum length of data in every column of a table passed as a parameter.
  
  Some notes for the script:
  
  LTRIM function can be used with m&hellip;
url: /index.php/datamgmt/datadesign/maximum-length-of-data-in/
views:
  - 16773
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
In this short blog post I would like to share a script I wrote yesterday as an answer to [this MSDN thread][1] to find maximum length of data in every column of a table passed as a parameter.

Some notes for the script:

LEN function can be used with many SQL Server types excluding text and new types such as Geography or HierarchyID. I added exclusion of these types directly in the query, there may be some other types which need to be also excluded.

I am using one of my favorite ideas of generating script to run using available meta-data.

sql
USE AdventureWorks2008R2

--declare @TableName sysname = 'Employee', @TableSchema sysname = 'HumanResources'
DECLARE @TableName sysname = 'Address', @TableSchema sysname = 'Person'
DECLARE @SQL NVARCHAR(MAX)

SELECT @SQL = STUFF((SELECT 
'
UNION ALL 
select ' + QUOTENAME(Table_Name,'''') + ' AS Table_Name, ' + 
QUOTENAME(Column_Name,'''') + ' AS ColumnName, MAX(LEN(' + QUOTENAME(Column_Name) + 
')) as [Max Length], ' + QUOTENAME(C.DATA_TYPE,'''') + ' AS Data_Type, ' + 
CAST(COALESCE(C.CHARACTER_MAXIMUM_LENGTH, C.NUMERIC_SCALE,0) AS VARCHAR(10)) + 
'  AS Data_Width FROM ' + QUOTENAME(TABLE_SCHEMA) + '.' + QUOTENAME(Table_Name)
FROM INFORMATION_SCHEMA.COLUMNS C 
WHERE TABLE_NAME = @TableName 
AND table_schema = @TableSchema
AND DATA_TYPE NOT IN ('text','ntext','XML','HierarchyID','Geometry','Geography')
ORDER BY COLUMN_NAME 
FOR XML PATH(''),Type).value('.','varchar(max)'),1,11,'')  
--print @SQL
EXECUTE (@SQL)
```

Here is another version of the same script which attempts to list every column in a table but uses DATALENGTH function instead of LEN for the types where we can not use LEN function:

sql
USE AdventureWorks2008R2
--declare @TableName sysname = 'Employee', @TableSchema sysname = 'HumanResources'
DECLARE @TableName sysname = 'Address', @TableSchema sysname = 'Person'
DECLARE @SQL NVARCHAR(MAX)

SELECT @SQL = STUFF((SELECT 
'
UNION ALL 
select ' + QUOTENAME(Table_Name,'''') + ' AS Table_Name, ' + 
QUOTENAME(Column_Name,'''') + ' AS ColumnName, MAX(' + 
CASE WHEN DATA_TYPE IN ('XML','HierarchyID','Geometry','Geography','text','ntext')
     THEN 'DATALENGTH(' ELSE 'LEN(' END + QUOTENAME(Column_Name) + 
')) as [Max Length], ' + QUOTENAME(C.DATA_TYPE,'''') + ' AS Data_Type, ' + 
CAST(COALESCE(C.CHARACTER_MAXIMUM_LENGTH, C.NUMERIC_SCALE,0) AS VARCHAR(10)) + 
'  AS Data_Width FROM ' + QUOTENAME(TABLE_SCHEMA) + '.' + QUOTENAME(Table_Name)
FROM INFORMATION_SCHEMA.COLUMNS C 
WHERE TABLE_NAME = @TableName 
AND table_schema = @TableSchema
--AND DATA_TYPE NOT IN ('XML','HierarchyID','Geometry','Geography')
ORDER BY COLUMN_NAME 
FOR XML PATH(''),Type).value('.','varchar(max)'),1,11,'')  
--print @SQL
EXECUTE (@SQL)
```
\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/d2d765e2-7994-4b3e-826b-c49c12f3b6fe
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22