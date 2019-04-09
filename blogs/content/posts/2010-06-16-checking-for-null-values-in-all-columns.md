---
title: Checking for NULL values in all columns that allow NULLS in all the tables
author: SQLDenis
type: post
date: 2010-06-16T14:39:00+00:00
ID: 822
url: /index.php/datamgmt/dbprogramming/checking-for-null-values-in-all-columns/
views:
  - 21641
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - howto
  - sql server 2005
  - sql server 2008
  - t-sql
  - tip

---
This post is based on a question I answered earlier today, someone wanted to check all columns that allow NULL for NULL values in all tables. The reason people might want to do this check is that they want to make all the columns not nullable in a database (after all we all know that developers hate NULLs). 

The Stored Procedure below is based on the code that [George Mastros][1] wrote for the following blog post: [Search all columns in all the tables in a database for a specific value][2]

sql
CREATE PROCEDURE FindColumnsWithNulls
AS
SET NOCOUNT ON
 
DECLARE @Temp TABLE(RowId INT IDENTITY(1,1), 
                    SchemaName sysname, 
                    TableName sysname, 
                    ColumnName SysName, 
                    DataType VARCHAR(100), 
                    DataFound BIT)
 
    --grab the columns that we care about
    INSERT  INTO @Temp(TableName,SchemaName, ColumnName, DataType)
    SELECT  C.Table_Name,C.TABLE_SCHEMA, C.Column_Name, C.Data_Type
    FROM    Information_Schema.Columns AS C
            INNER Join Information_Schema.Tables AS T
                ON C.Table_Name = T.Table_Name
        AND C.TABLE_SCHEMA = T.TABLE_SCHEMA
    WHERE   Table_Type = 'Base Table'  --only tables, no views
    AND C.IS_NULLABLE = 'YES'  --obviously only check nullable columns
            
 
 
DECLARE @i INT
DECLARE @MAX INT
DECLARE @TableName sysname
DECLARE @ColumnName sysname
DECLARE @SchemaName sysname
DECLARE @SQL NVARCHAR(4000)
DECLARE @PARAMETERS NVARCHAR(4000)
DECLARE @DataExists BIT
DECLARE @SQLTemplate NVARCHAR(4000)
 
SELECT  @SQLTemplate = 'If Exists(Select 1
                                          From   ReplaceTableName
                                          Where  [ReplaceColumnName] IS NULL)
                                     Set @DataExists = 1
                                 Else
                                     Set @DataExists = 0',
        @PARAMETERS = '@DataExists Bit OUTPUT',
        @i = 1
 
SELECT @i = 1, @MAX = MAX(RowId)
FROM   @Temp
 --loop over all the columns 
WHILE @i <= @MAX
    BEGIN
        --change the place holder with the real name 
        SELECT  @SQL = REPLACE(REPLACE(@SQLTemplate, 'ReplaceTableName', 
                       QUOTENAME(SchemaName) + '.' + 
                       QUOTENAME(TableName)), 'ReplaceColumnName', ColumnName)
        FROM    @Temp
        WHERE   RowId = @i
 
 
        -- PRINT @SQL -- poor man's debugger  🙂

        EXEC SP_EXECUTESQL @SQL, @PARAMETERS, @DataExists = @DataExists OUTPUT
 
        --update result table if a NULL is found
        IF @DataExists =1
            UPDATE @Temp SET DataFound = 1 WHERE RowId = @i
 
        SET @i = @i + 1
    END
 
--Report to the user how many columns have NULLS
SELECT  SchemaName,TableName, ColumnName,DataType
FROM    @Temp
WHERE   DataFound = 1
```
You can call the code like this

sql
exec FindColumnsWithNulls
```

You will get a &#8216;report' that lists SchemaName, TableName, ColumnName and DataType

This proc does not tell you how many NULLS you have in a column, it will just report that the column has at least one NULL value. With the output from the proc, it is pretty easy for you to find how many NULLS there are and then update the value to something that is not NULL

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: /index.php/All/?disp=authdir&author=10
 [2]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#2
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22