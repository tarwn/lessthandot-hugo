---
title: The Ten Most Asked SQL Server Questions And Their Answers
author: SQLDenis
type: post
date: 2008-12-10T15:09:00+00:00
ID: 241
excerpt: 'If you are active in the SQL Server newsgroups and forums as I am, you will notice that the same questions keep popping up all the time. I picked ten of them which I see daily. Since this became a pretty long blogpost I have linked all the questions bel&hellip;'
url: /index.php/datamgmt/datadesign/the-ten-most-asked-sql-server-questions-1/
aliases:
  - /index.php/datamgmt/datadesign/the-ten-most-asked-sql-server-questions--1/
views:
  - 126983
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - answers
  - questions
  - sql server 2000
  - sql server 2005
  - sql server 2008

---
If you are active in the SQL Server newsgroups and forums as I am, you will notice that the same questions keep popping up all the time. I picked ten of them which I see daily. Since this became a pretty long blogpost I have linked all the questions below to jump to the question itself.

1) [Selecting all the values from a table for a particular date][1]
  
2) [Search all columns in all the tables in a database for a specific value][2]
  
3) [Splitting string values][3]
  
4) [Select all rows from one table that don't exist in another table][4]
  
5) [Getting all rows from one table and only the latest from the child table][5]
  
6) [Getting all characters until a specific character][6]
  
7) [Return all rows with NULL values in a column][7]
  
8) [Row values to column (PIVOT)][8]
  
9) [Pad or remove leading zeroes from numbers][9]
  
10) [Concatenate Values From Multiple Rows Into One Column][10]

## 1 Selecting all the values from a table for a particular date {#1}

This is a very popular question and people sometimes answer that you need to use between. There is a problem with between if you happen to have a value at exactly midnight. Let us take a look, first create this table.

```sql
CREATE TABLE SomeDates (DateColumn DATETIME)
```

Insert 2 values

```sql
INSERT INTO SomeDates VALUES('2008-10-02 00:00:00.000')
INSERT INTO SomeDates VALUES('2008-10-01 00:00:00.000')
```

Return everything between '2008-10-01' and '2008-10-02'

```sql
SELECT *
FROM SomeDates
WHERE DateColumn BETWEEN '20081001' AND '20081002'
ORDER BY DateColumn
```

This works without a problem, we get this returned

(results)
  
2008-10-01 00:00:00.000
  
2008-10-02 00:00:00.000

Let's add some more dates including the time portion

```sql
INSERT INTO SomeDates VALUES('2008-10-02 00:01:00.000')
INSERT INTO SomeDates VALUES('2008-10-02 00:00:59.000')
INSERT INTO SomeDates VALUES('2008-10-02 00:00:01.000')
INSERT INTO SomeDates VALUES('2008-10-01 00:01:00.000')
INSERT INTO SomeDates VALUES('2008-10-01 00:12:00.000')
INSERT INTO SomeDates VALUES('2008-10-01 23:00:00.000')
```

Return everything between '2008-10-01' and '2008-10-02'

```sql
SELECT *
FROM SomeDates
WHERE DateColumn BETWEEN '20081001' AND '20081002'
ORDER BY DateColumn
```

(results)
  
2008-10-01 00:00:00.000
  
2008-10-01 00:01:00.000
  
2008-10-01 00:12:00.000
  
2008-10-01 23:00:00.000
  
2008-10-02 00:00:00.000

Here is where it goes wrong; for 2008-10-02 only the midnight value is returned the other ones are ignored

Now if we change 2008-10-02 to 2008-10-03 we get what we want

```sql
SELECT *
FROM SomeDates
WHERE DateColumn BETWEEN '20081001' AND '20081003'
ORDER BY DateColumn
```
(results)
  
2008-10-01 00:00:00.000
  
2008-10-01 00:01:00.000
  
2008-10-01 00:12:00.000
  
2008-10-01 23:00:00.000
  
2008-10-02 00:00:00.000
  
2008-10-02 00:00:01.000
  
2008-10-02 00:00:59.000
  
2008-10-02 00:01:00.000

Now insert a value for 2008-10-03 (midnight)

```sql
INSERT INTO SomeDates VALUES('2008-10-03 00:00:00.000')
```

Run the query again

```sql
SELECT *
FROM SomeDates
WHERE DateColumn BETWEEN '20081001' AND '20081003'
ORDER BY DateColumn
```

(results)
  
2008-10-01 00:00:00.000
  
2008-10-01 00:01:00.000
  
2008-10-01 00:12:00.000
  
2008-10-01 23:00:00.000
  
2008-10-02 00:00:00.000
  
2008-10-02 00:00:01.000
  
2008-10-02 00:00:59.000
  
2008-10-02 00:01:00.000
  
2008-10-03 00:00:00.000

We get back 2008-10-03 00:00:00.000, between will return the date if it is exactly midnight

If you use >= and < then you get exactly what you need 

```sql
SELECT *
FROM SomeDates
WHERE DateColumn >= '20081001' AND DateColumn < '20081003'
ORDER BY DateColumn
```

(results)
  
2008-10-01 00:00:00.000
  
2008-10-01 00:01:00.000
  
2008-10-01 00:12:00.000
  
2008-10-01 23:00:00.000
  
2008-10-02 00:00:00.000
  
2008-10-02 00:00:01.000
  
2008-10-02 00:00:59.000
  
2008-10-02 00:01:00.000

So be careful when using between because you might get back rows that you did not expect to get back and it might mess up your reporting if you do counts or sums

**Bonus: how to strip the time off a date?**

To strip the time portion of a datetime, you can do this and still return a datetime

```sql
Select * ,DATEADD(dd, DATEDIFF(dd, 0, DateColumn), 0) as stripped
from SomeDates 
```

you can also do a convert to varchar with a style value of 112 and then converting back to datetime

```sql
Select * ,convert(datetime,Convert(varchar(8),DateColumn,112)) as stripped
from SomeDates 
```

Both methods return this
  
———————————————–

<pre>DateColumn              stripped
2008-10-02 00:00:00.000	2008-10-02 00:00:00.000
2008-10-01 00:00:00.000	2008-10-01 00:00:00.000
2008-10-02 00:01:00.000	2008-10-02 00:00:00.000
2008-10-02 00:00:59.000	2008-10-02 00:00:00.000
2008-10-02 00:00:01.000	2008-10-02 00:00:00.000
2008-10-01 00:01:00.000	2008-10-01 00:00:00.000
2008-10-01 00:12:00.000	2008-10-01 00:00:00.000
2008-10-01 23:00:00.000	2008-10-01 00:00:00.000
2008-10-03 00:00:00.000	2008-10-03 00:00:00.000</pre>

## 2 Search all columns in all the tables in a database for a specific value {#2}

How can I search all the columns in every table in my database for a specific value?
  
This question keeps popping up all the time. Here is a variation of this question discussed [MSDN Forum T-SQL][11]. Unfortunately there is no easy way to do this, you have to loop over all the tables in the database and search for the value.
  
As you can imagine if you have a huge database with a lot of tables this can take a really long time.
  
There are a couple of checks going on in this proc, if the data is not numeric then we skip all the numeric columns and do not search those columns. This code was created by our own gmmastros and what he did was create 3 different stored procedures, one for dates, numbers and strings. Then he created a 4th stored proc named FindMyData, this proc will only call the appropriate child procs if the datatype is right.
  
We only list the table name and the column name where the value is stored, we do not return that data!
  
First create the following 3 stored procedures

Just the date searching proc

```sql
CREATE PROCEDURE FindMyData_Date
    @DataToFind DATETIME
AS
SET NOCOUNT ON
 
DECLARE @Temp TABLE(RowId INT IDENTITY(1,1), SchemaName sysname, TableName sysname, ColumnName SysName, DataType VARCHAR(100), DataFound BIT)

DECLARE @ISDATE BIT
 

 
    INSERT  INTO @Temp(TableName,SchemaName, ColumnName, DataType)
    SELECT  C.Table_Name,C.TABLE_SCHEMA, C.Column_Name, C.Data_Type
    FROM    Information_Schema.Columns AS C
            INNER Join Information_Schema.Tables AS T
                ON C.Table_Name = T.Table_Name
		AND C.TABLE_SCHEMA = T.TABLE_SCHEMA
    WHERE   Table_Type = 'Base Table'
            And (Data_Type = 'DateTime'
            Or (Data_Type = 'SmallDateTime' And @DataToFind >= '19000101' And @DataToFind < '20790607'))
 
DECLARE @i INT
DECLARE @MAX INT
DECLARE @TableName sysname
DECLARE @ColumnName sysname
DECLARE @SchemaName sysname
DECLARE @SQL NVARCHAR(4000)
DECLARE @PARAMETERS NVARCHAR(4000)
DECLARE @DataExists BIT
DECLARE @SQLTemplate NVARCHAR(4000)
 
SELECT  @SQLTemplate = 'If Exists(Select *
                                 From   ReplaceTableName
                                 Where  [ReplaceColumnName]
                                              = ''' + CONVERT(VARCHAR(30), @DataToFind, 126) + '''
                                 )
                           Set @DataExists = 1
                       Else
                           Set @DataExists = 0',
        @PARAMETERS = '@DataExists Bit OUTPUT',
        @i = 1
 
SELECT @i = 1, @MAX = MAX(RowId)
FROM   @Temp
 
WHILE @i <= @MAX
    BEGIN
        SELECT  @SQL = REPLACE(REPLACE(@SQLTemplate, 'ReplaceTableName', QUOTENAME(SchemaName) + '.' + QUOTENAME(TableName)), 'ReplaceColumnName', ColumnName)
        FROM    @Temp
        WHERE   RowId = @i
 
 
        PRINT @SQL
        EXEC SP_EXECUTESQL @SQL, @PARAMETERS, @DataExists = @DataExists OUTPUT
 
        IF @DataExists =1
            UPDATE @Temp SET DataFound = 1 WHERE RowId = @i
 
        SET @i = @i + 1
    END
 
SELECT  SchemaName,TableName, ColumnName
FROM    @Temp
WHERE   DataFound = 1

go
```

If you want to test just this proc, try this

```sql
exec FindMyData_Date '20070615'
```

This is the string proc

```sql
CREATE PROCEDURE FindMyData_String
    @DataToFind NVARCHAR(4000),
    @ExactMatch BIT = 0
AS
SET NOCOUNT ON
 
DECLARE @Temp TABLE(RowId INT IDENTITY(1,1), SchemaName sysname, TableName sysname, ColumnName SysName, DataType VARCHAR(100), DataFound BIT)
 
    INSERT  INTO @Temp(TableName,SchemaName, ColumnName, DataType)
    SELECT  C.Table_Name,C.TABLE_SCHEMA, C.Column_Name, C.Data_Type
    FROM    Information_Schema.Columns AS C
            INNER Join Information_Schema.Tables AS T
                ON C.Table_Name = T.Table_Name
		AND C.TABLE_SCHEMA = T.TABLE_SCHEMA
    WHERE   Table_Type = 'Base Table'
            And Data_Type In ('ntext','text','nvarchar','nchar','varchar','char')
 
 
DECLARE @i INT
DECLARE @MAX INT
DECLARE @TableName sysname
DECLARE @ColumnName sysname
DECLARE @SchemaName sysname
DECLARE @SQL NVARCHAR(4000)
DECLARE @PARAMETERS NVARCHAR(4000)
DECLARE @DataExists BIT
DECLARE @SQLTemplate NVARCHAR(4000)
 
SELECT  @SQLTemplate = CASE WHEN @ExactMatch = 1
                            THEN 'If Exists(Select *
                                           From   ReplaceTableName
                                           Where  Convert(nVarChar(4000), [ReplaceColumnName])
                                                        = ''' + @DataToFind + '''
                                           )
                                      Set @DataExists = 1
                                  Else
                                      Set @DataExists = 0'
                            ELSE 'If Exists(Select *
                                           From   ReplaceTableName
                                           Where  Convert(nVarChar(4000), [ReplaceColumnName])
                                                        Like ''%' + @DataToFind + '%''
                                           )
                                      Set @DataExists = 1
                                  Else
                                      Set @DataExists = 0'
                            END,
        @PARAMETERS = '@DataExists Bit OUTPUT',
        @i = 1
 
SELECT @i = 1, @MAX = MAX(RowId)
FROM   @Temp
 
WHILE @i <= @MAX
    BEGIN
        SELECT  @SQL = REPLACE(REPLACE(@SQLTemplate, 'ReplaceTableName', QUOTENAME(SchemaName) + '.' + QUOTENAME(TableName)), 'ReplaceColumnName', ColumnName)
        FROM    @Temp
        WHERE   RowId = @i
 
 
        PRINT @SQL
        EXEC SP_EXECUTESQL @SQL, @PARAMETERS, @DataExists = @DataExists OUTPUT
 
        IF @DataExists =1
            UPDATE @Temp SET DataFound = 1 WHERE RowId = @i
 
        SET @i = @i + 1
    END
 
SELECT  SchemaName,TableName, ColumnName
FROM    @Temp
WHERE   DataFound = 1
GO
```

If you want to test just this proc, try this

```sql
exec FindMyData_string 'google', 0
```

Just the number proc

```sql
CREATE PROCEDURE FindMyData_Number
    @DataToFind NVARCHAR(4000),
    @ExactMatch BIT = 0
AS
SET NOCOUNT ON
DECLARE @Temp TABLE(RowId INT IDENTITY(1,1), SchemaName sysname, TableName sysname, ColumnName SysName, DataType VARCHAR(100), DataFound BIT)

DECLARE @IsNumber BIT
DECLARE @ISDATE BIT
 
IF ISNUMERIC(CONVERT(VARCHAR(20), @DataToFind)) = 1
    SET @IsNumber = 1
ELSE
    SET @IsNumber = 0
 
    INSERT  INTO @Temp(TableName,SchemaName, ColumnName, DataType)
    SELECT  C.Table_Name,C.TABLE_SCHEMA, C.Column_Name, C.Data_Type
    FROM    Information_Schema.Columns AS C
            INNER Join Information_Schema.Tables AS T
                ON C.Table_Name = T.Table_Name
        AND C.TABLE_SCHEMA = T.TABLE_SCHEMA
    WHERE   Table_Type = 'Base Table'
            And Data_Type In ('float','real','decimal','money','smallmoney','bigint','int','smallint','tinyint','bit')
 
 
DECLARE @i INT
DECLARE @MAX INT
DECLARE @TableName sysname
DECLARE @ColumnName sysname
DECLARE @SQL NVARCHAR(4000)
DECLARE @PARAMETERS NVARCHAR(4000)
DECLARE @DataExists BIT
DECLARE @SQLTemplate NVARCHAR(4000)
 
SELECT  @SQLTemplate = CASE WHEN @ExactMatch = 1
                            THEN 'If Exists(Select *
                                           From   ReplaceTableName
                                           Where  Convert(VarChar(40), [ReplaceColumnName])
                                                        = ''' + @DataToFind + '''
                                           )
                                      Set @DataExists = 1
                                  Else
                                      Set @DataExists = 0'
                            ELSE 'If Exists(Select *
                                           From   ReplaceTableName
                                           Where  Convert(VarChar(40), [ReplaceColumnName])
                                                        Like ''%' + @DataToFind + '%''
                                           )
                                      Set @DataExists = 1
                                  Else
                                      Set @DataExists = 0'
                            END,
        @PARAMETERS = '@DataExists Bit OUTPUT',
        @i = 1
 
SELECT @i = 1, @MAX = MAX(RowId)
FROM   @Temp
 
WHILE @i <= @MAX
    BEGIN
        SELECT  @SQL = REPLACE(REPLACE(@SQLTemplate, 'ReplaceTableName', QUOTENAME(SchemaName) + '.' + QUOTENAME(TableName)), 'ReplaceColumnName', ColumnName)
	FROM    @Temp
        WHERE   RowId = @i
 
 
        PRINT @SQL
        EXEC SP_EXECUTESQL @SQL, @PARAMETERS, @DataExists = @DataExists OUTPUT
 
        IF @DataExists =1
            UPDATE @Temp SET DataFound = 1 WHERE RowId = @i
 
        SET @i = @i + 1
    END
 
SELECT  SchemaName,TableName, ColumnName
FROM    @Temp
WHERE   DataFound = 1
 
go
```

If you want to test just this proc, try this

```sql
exec FindMyData_Number '562', 1
```

The mother of all procs!

```sql
CREATE PROCEDURE FindMyData
    @DataToFind NVARCHAR(4000),
    @ExactMatch BIT = 0
AS
SET NOCOUNT ON
 
CREATE TABLE #Output(SchemaName sysname, TableName sysname, ColumnName sysname)
 
IF ISDATE(@DataToFind) = 1
    INSERT INTO #Output EXEC FindMyData_Date @DataToFind
 
IF ISNUMERIC(@DataToFind) = 1
    INSERT INTO #Output EXEC FindMyData_Number @DataToFind, @Exactmatch
 
INSERT INTO #Output EXEC FindMyData_String @DataToFind, @ExactMatch
 
SELECT SchemaName,TableName, ColumnName
FROM   #Output
ORDER BY SchemaName,TableName, ColumnName
go
```

Here are some proc calls to test it out

```sql
exec FindMyData 'google', 0
exec FindMyData 1, 0
exec FindMyData '20081201', 0
```

```sql
exec FindMyData 'sysobjects', 0 
```

## 3 Splitting string values {#3}

The fastest way to split a comma delimited string is by using a number table. If you do not have a number table in your database then use this code to create one

```sql
-- Create our Pivot table ** do this only once
    CREATE TABLE NumberPivot (NumberID INT PRIMARY KEY)
    
       INSERT INTO NumberPivot
       SELECT number FROM master..spt_values
	where type = 'P'
   
    GO
```

Now you can run the following code to return each of the values in the comma delimited string in its own row

```sql
DECLARE @SplitString VARCHAR(1000)
    SELECT @SplitString ='1,4,77,88,4546,234,2,3,54,87,9,6,4,36,6,9,9,6,4,4,68,9,0,5,3,2,'
     
    SELECT SUBSTRING(',' + @SplitString + ',', NumberID + 1,
    CHARINDEX(',', ',' + @SplitString + ',', NumberID + 1) - NumberID -1)AS VALUE
    FROM NumberPivot
    WHERE NumberID <= LEN(',' + @SplitString + ',') - 1
    AND SUBSTRING(',' + @SplitString + ',', NumberID, 1) = ','
    GO
```

You can also return distinct values by using DISTINCT 

```sql
DECLARE @SplitString VARCHAR(1000)
    SELECT @SplitString ='1,4,77,88,4546,234,2,3,54,87,9,6,4,36,6,9,9,6,4,4,68,9,0,5,3,2'
     
    SELECT DISTINCT SUBSTRING(',' + @SplitString + ',', NumberID + 1,
    CHARINDEX(',', ',' + @SplitString + ',', NumberID + 1) - NumberID -1)AS VALUE
    FROM NumberPivot
    WHERE NumberID <= LEN(',' + @SplitString + ',') - 1
    AND SUBSTRING(',' + @SplitString + ',', NumberID, 1) = ','
```

You now can dump the result into a table and then you can join one of your real tables with that table which will execute much faster

## 4 Select all rows from one table that don't exist in another table {#4}

There are at least 5 ways to return data from one table which is not in another table. Two of these are SQL Server 2005 and greater only

NOT IN
  
NOT EXISTS
  
LEFT and RIGHT JOIN
  
OUTER APPLY (2005+)
  
EXCEPT (2005+)

First Create these two tables

```sql
CREATE TABLE testnulls (ID INT)
    INSERT INTO testnulls VALUES (1)
    INSERT INTO testnulls VALUES (2)
    INSERT INTO testnulls VALUES (null)
 
    CREATE TABLE testjoin (ID INT)
    INSERT INTO testjoin VALUES (1)
    INSERT INTO testjoin VALUES (3)
```

**NOT IN**

```sql
SELECT * FROM testjoin WHERE ID NOT IN(SELECT ID FROM testnulls)
```

What happened? Nothing gets returned! The reason is because the subquery returns a NULL and you can't compare a NULL to anything

Now run this

```sql
SELECT * FROM testjoin WHERE ID NOT IN(SELECT ID FROM testnulls WHERE ID IS NOT NULL)
```

That worked because we eliminated the NULL values in the subquery

**NOT EXISTS**

NOT EXISTS doesn't have the problem that NOT IN has. Run the following code

```sql
SELECT * FROM testjoin j
    WHERE NOT EXISTS (SELECT n.ID
    FROM testnulls n
    WHERE n.ID = j.ID)
```

Everything worked as expected

**LEFT and RIGHT JOIN**

Plain vanilla LEFT and RIGHT JOINS

```sql
SELECT j.* FROM testjoin j
    LEFT OUTER JOIN testnulls n ON n.ID = j.ID
    WHERE n.ID IS NULL
 
    SELECT j.* FROM  testnulls n
    RIGHT OUTER JOIN testjoin j  ON n.ID = j.ID
    WHERE n.ID IS NULL
```

**OUTER APPLY (SQL 2005 +)**

OUTER APPLY is something that got added to SQL 2005

```sql
SELECT j.* FROM testjoin j
    OUTER APPLY
    (SELECT id  FROM testnulls n
    WHERE n.ID = j.ID) a
    WHERE a.ID IS NULL
```

**EXCEPT(SQL 2005 +)**
  
EXCEPT is something that got added to SQL 2005. It basically returns everything from the top table which is not in the bottom table

```sql
SELECT * FROM testjoin
    EXCEPT
    SELECT * FROM testnulls
```

**INTERSECT** 
  
INTERSECT returns what ever is in both tables(like a regular join)

```sql
SELECT * FROM testjoin
    INTERSECT
    SELECT * FROM testnulls
```

## 5 Getting all rows from one table and only the latest from the child table  {#5}

First create this table

```sql
CREATE TABLE #MaxVal(id INT,VALUE INT,SomeDate datetime)
    INSERT #MaxVal VALUES(1,1,'20010101')
    INSERT #MaxVal VALUES(1,2,'20020101')
    INSERT #MaxVal VALUES(1,3,'20080101')
    INSERT #MaxVal VALUES(2,1,'20010101')
    INSERT #MaxVal VALUES(2,2,'20060101')
    INSERT #MaxVal VALUES(2,3,'20080101')
    INSERT #MaxVal VALUES(3,1,'20010101')
    INSERT #MaxVal VALUES(3,2,'20080101')
```

If you just need the max value from a column you can just do a group by

```sql
SELECT id,MAX(SomeDate) AS VALUE
    FROM #MaxVal
    GROUP BY id
```

If you need the whole row back then the query below is one way of doing this

```sql
SELECT t.* FROM(
    SELECT id,MAX(SomeDate) AS MaxValue
    FROM #MaxVal
    GROUP BY id) x
    JOIN #MaxVal t ON x.id =t.id
    AND x.MaxValue =t.SomeDate
```

We have another blog post on our site which has a lot more detail about doing this, as a matter of fact that blog post describes 5 ways to do it. These 5 ways are:

**Key Search**
  
1. Correlated Subquery
  
2. Derived Table

**Number and Filter**
  
3. Windowing Function – Two Stage
  
4. Windowing Function – One Stage

**Simulate MS Access**
  
5. Compound Key, aka Packed Values

That post can be found here: [Including an Aggregated Column's Related Values][12]

## 6 Getting all characters until a specific character (charindex + left) {#6}

There are two built in functions that you can use in SQL Server to return the first position in a column of the character you are looking for. These functions are PATINDEX and CHARINDEX. Let's take a look at how PATINDEX works. First create this table.

```sql
CREATE TABLE #SomeTable2
(SomeValue VARCHAR(49))
INSERT INTO #SomeTable2 VALUES ('one two three')
INSERT INTO #SomeTable2 VALUES ('1 2 3')
INSERT INTO #SomeTable2 VALUES ('abc def ghi')
INSERT INTO #SomeTable2 VALUES ('one two')
INSERT INTO #SomeTable2 VALUES ('one two three four')
INSERT INTO #SomeTable2 VALUES ('one two three four five')
```

Now we want to return everything up to the first space and also everything after the last space. Here is how we do that

```sql
select *,left(SomeValue,patindex('% %',SomeValue)-1),
right(SomeValue,patindex('% %',(reverse(SomeValue)))-1)
from #SomeTable2
```

(results)<table border = "1"> 

</table> 

As you can see to get the last value you can just use the REVERSE function with the RIGHT function. 

## 7 Return all rows with NULL values in a column {#7}

People have a hard time with nulls, the first problem with NULLs is that your WHERE clause is not the same as if you would select a blank or a value 

First create this simple table

```sql
CREATE TABLE #SomeTableNull(SomeValue VARCHAR(49))
INSERT INTO #SomeTableNull VALUES ('1')
INSERT INTO #SomeTableNull VALUES ('')
INSERT INTO #SomeTableNull VALUES ('a')
INSERT INTO #SomeTableNull VALUES (NULL)
```

To search a column your WHERE clause uses the = sign

```sql
SELECT * FROM #SomeTableNull WHERE SomeValue = ''
SELECT * FROM #SomeTableNull WHERE SomeValue = '1'
SELECT * FROM #SomeTableNull WHERE SomeValue = 'a'
```

Now let us try that to find a NULL value

```sql
SELECT * FROM #SomeTableNull WHERE SomeValue = NULL
```

What just happened? We got nothing back? The reason is that you cannot compare a NULL value according to ANSI standards.

```sql
Take a look at this
IF NULL = NULL
print 'equal'
else 
print 'not so equal'
```

The bottom print statement got printed, NULL is unknown and you do not know if two unknows are the same

The correct way to return the NULL value is the following

```sql
SELECT * FROM #SomeTableNull WHERE SomeValue IS NULL
```

Just so that you know this, if you turn off ansi nulls then you can use =

```sql
SET ansi_nulls off


SELECT * FROM #SomeTableNull WHERE SomeValue = NULL

SET ansi_nulls on
```

However I do not recommend doing that ever, the default is ON and I would leave it like that

## 8 Row values to column (PIVOT) {#8}

A very frequent request is how to pivot/transpose/crosstab a query. SQL server 2005 introduced PIVOT, this makes life a lot easier compared to the SQL 2000 days. so let's see how this works. First create this table

```sql
CREATE TABLE #SomeTable
(SomeName VARCHAR(49), Quantity INT)
```

```sql
INSERT INTO #SomeTable VALUES ('Scarface', 2)
INSERT INTO #SomeTable VALUES ('Scarface', 4)
INSERT INTO #SomeTable VALUES ('LOTR', 5)
INSERT INTO #SomeTable VALUES ('LOTR', 6)
INSERT INTO #SomeTable VALUES ( 'Jaws', 2)
INSERT INTO #SomeTable VALUES ('Blade', 5)
INSERT INTO #SomeTable VALUES ('Saw', 6)
INSERT INTO #SomeTable VALUES ( 'Saw', 2)
INSERT INTO #SomeTable VALUES ( 'Jaws', 12)
INSERT INTO #SomeTable VALUES ('Blade', 5)
INSERT INTO #SomeTable VALUES ('Saw', 6)
INSERT INTO #SomeTable VALUES ( 'Saw', 2)
```

What we want is too list all the movies in a column and the sum of all quantities for that movie as a value. So in this case we want this output

<table border="1">
  <tr>
    <td>
      Scarface
    </td>
    
    <td>
      LOTR
    </td>
    
    <td>
      Jaws
    </td>
    
    <td>
      Saw
    </td>
    
    <td>
      Blade
    </td>
  </tr>
  
  <tr>
    <td>
      6
    </td>
    
    <td>
      11
    </td>
    
    <td>
      14
    </td>
    
    <td>
      16
    </td>
    
    <td>
      10
    </td>
  </tr>
</table>

First let's look how we can do this in SQL Server 2000, this BTW will also work in SQL Server 2005/2008

```sql
SELECT SUM(CASE SomeName WHEN 'Scarface' THEN Quantity ELSE 0 END) AS Scarface,
SUM(CASE SomeName WHEN 'LOTR' THEN Quantity ELSE 0 END) AS LOTR,
SUM(CASE SomeName WHEN 'Jaws' THEN Quantity ELSE 0 END) AS Jaws,
SUM(CASE SomeName WHEN 'Saw' THEN Quantity ELSE 0 END) AS Saw,
SUM(CASE SomeName WHEN 'Blade' THEN Quantity ELSE 0 END) AS Blade
FROM #SomeTable
```

In SQL Server 2005/2008 we can use PIVOT, here is how we can use it

```sql
SELECT Scarface, LOTR, Jaws, Saw,Blade
FROM
(SELECT SomeName,Quantity
FROM #SomeTable) AS pivTemp
PIVOT
(   SUM(Quantity)
    FOR SomeName IN (Scarface, LOTR, Jaws, Saw,Blade)
) AS pivTable
```

That looks a little bit neater than the SQL 2000 version.
  
If you are interested in Column To Row take a look at [Column To Row (UNPIVOT)][13] on our wiki

## 9 Pad or remove leading zeroes from numbers {#9}

To pad a number with leading zeroes you use as many zeroes as you want the output to be, then use concatenation plus the right function to display it
  
For example if you want to pad a 6 digit column with zeroes you would do something like this
   
RIGHT('000000' + CONVERT(VARCHAR(6),ColumnName),6)

Here is some code that will show this, first create this table
  
USE tempdb
  
go

```sql
CREATE TABLE testpadding(id VARCHAR(50),id2 INT)
INSERT testpadding VALUES('000001',1)
INSERT testpadding VALUES('000134',134)
INSERT testpadding VALUES('002232',2232)
INSERT testpadding VALUES('000002',2)
```

Now run 

```sql
SELECT RIGHT('000000' + CONVERT(VARCHAR(6),id2),6)
FROM testpadding
```

(results)
  
000001
  
000134
  
002232
  
000002

what about the id columns and stripping the zeroes from that?
  
No problem do this

```sql
SELECT CONVERT(INT,id)
FROM testpadding
```

(results)
  
1
  
134
  
2232
  
2

Beautiful right? Not so fast, insert this row

```sql
INSERT testpadding VALUES('02222222222222222222200002',2)
```

```sql
SELECT CONVERT(INT,id)
FROM testpadding
```

Server: Msg 248, Level 16, State 1, Line 1
  
The conversion of the varchar value '02222222222222222222200002' overflowed an int column.

Okay we can do a bigint instead

```sql
SELECT CONVERT(bigINT,id)
FROM testpadding
```

Server: Msg 8115, Level 16, State 2, Line 1
  
Arithmetic overflow error converting expression to data type bigint.

Nope, even that doesn't fit, now what?

```sql
select replace(ltrim(replace(id,'0',' ')),' ','0')
FROM testpadding
```

(results)
  
1
  
134
  
2232
  
2
  
2222222222222222222200002

So how does that work? First you replace all the zeroes with spaces, then you trim it to get rid of the leading spaces, after you replace the space back to zeroes again, the leading zeroes don't exists anymore because they were trimmed

## 10 Concatenate Values From Multiple Rows Into One Column {#10}

If you want to concatenate values from multiple rows into one and you want to order it then you have to use FOR XML PATH. The ORDER BY is -not- guaranteed to be processed before the concatenation occurs, if you use that technique. However, it -is- guaranteed if you use FOR XML PATH(”).

Let's take a look. First create these tables

```sql
USE TEMPDB
GO
CREATE TABLE Authors (Id INT, LastName VARCHAR(100), FirstName VARCHAR(100))
GO
INSERT Authors VALUES(1,'Nielsen','Paul')
INSERT Authors VALUES(2,'King','Stephen')
INSERT Authors VALUES(3,'Stephenson','Neal')
GO
 
CREATE TABLE Books (ID INT,AuthorID INT, BookName VARCHAR(200))
GO
INSERT Books VALUES(1,1,'SQL Server 2005 Bible')
INSERT Books VALUES(2,1,'SQL Server 2008 Bible')
 
INSERT Books VALUES(3,2,'It')
INSERT Books VALUES(4,2,'The Stand')
INSERT Books VALUES(5,2,'Thinner')
INSERT Books VALUES(6,2,'Salems Lot')
 
 
INSERT Books VALUES(7,3,'Snow Crash')
INSERT Books VALUES(8,3,'The Diamond Age')
INSERT Books VALUES(9,3,'Cryptonomicon')
GO
```

This is the old style function, it will run on SQL Server 2000 and up

```sql
CREATE FUNCTION fnGetBooks2 (@AuthorID INT)
RETURNS VARCHAR(8000)
AS
BEGIN
        DECLARE @BookList VARCHAR(8000)
        SELECT @BookList = ''
        SELECT @BookList = @BookList + BookName +','
        FROM Books
        WHERE AuthorID = @AuthorID
        AND BookName IS NOT NULL
        ORDER BY BookName --yes we can sort
 
RETURN LEFT(@BookList,(LEN(@BookList) -1))
END
GO
```

This is the same function using XML Path, so SQL Server 2005 and up

```sql
CREATE FUNCTION fnGetBooks (@AuthorID INT)
 
RETURNS VARCHAR(8000)
AS
BEGIN
       
 DECLARE @BookList VARCHAR(8000)
 SELECT @BookList =(
 
        SELECT  BookName + ', ' AS [text()]
 
     FROM    Books
 WHERE AuthorID = @AuthorID
        AND BookName IS NOT NULL
     ORDER BY BookName
 
     FOR XML PATH('') )
       
 
RETURN LEFT(@BookList,(LEN(@BookList) -1))
END
GO
```

Here are the calls to the functions

```sql
SELECT *,dbo.fnGetBooks(id) AS Books
FROM Authors
```

```sql
SELECT *,dbo.fnGetBooks2(id) AS Books
FROM Authors
```



(results)

<table border="1">
  <tr>
    <td>
      Id
    </td>
    
    <td>
      LastName
    </td>
    
    <td>
      FirstName
    </td>
    
    <td>
      Books
    </td>
  </tr>
  
  <tr>
    <td>
      1
    </td>
    
    <td>
      Nielsen
    </td>
    
    <td>
      Paul
    </td>
    
    <td>
      SQL Server 2005 Bible,SQL Server 2008 Bible
    </td>
  </tr>
  
  <tr>
    <td>
      2
    </td>
    
    <td>
      King
    </td>
    
    <td>
      Stephen
    </td>
    
    <td>
      It,Salems Lot,The Stand,Thinner
    </td>
  </tr>
  
  <tr>
    <td>
      3
    </td>
    
    <td>
      Stephenson
    </td>
    
    <td>
      Neal
    </td>
    
    <td>
      Cryptonomicon,Snow Crash,The Diamond Age
    </td>
  </tr>
</table>

The results are the same but I have been assured by people in the private SQL Server MVP group that FOR XML PATH will preserve the order while the other function might not

In case you want some more of this stuff we have over 80 hacks, tips and trick on our [SQL Server Programming Hacks][14] wiki page

\*** **Remember, if you have a SQL related question try our [Microsoft SQL Server Programming][15] forum or our [Microsoft SQL Server Admin][16] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#1
 [2]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#2
 [3]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#3
 [4]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#4
 [5]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#5
 [6]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#6
 [7]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#7
 [8]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#8
 [9]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#9
 [10]: /index.php/DataMgmt/DataDesign/the-ten-most-asked-sql-server-questions--1#10
 [11]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/190a0d12-840f-4ecb-8d32-b436743fdf4e
 [12]: /index.php/DataMgmt/DBProgramming/MSSQLServer/including-an-aggregated-column-s-related
 [13]: http://wiki.ltd.local/index.php/Column_To_Row_%28UNPIVOT%29
 [14]: http://wiki.ltd.local/index.php/SQL_Server_Programming_Hacks_-_100%2B_List
 [15]: http://forum.ltd.local/viewforum.php?f=17
 [16]: http://forum.ltd.local/viewforum.php?f=22