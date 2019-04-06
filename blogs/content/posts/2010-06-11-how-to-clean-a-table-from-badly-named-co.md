---
title: How to clean a table from badly named column names
author: SQLDenis
type: post
date: 2010-06-11T10:41:12+00:00
url: /index.php/datamgmt/datadesign/how-to-clean-a-table-from-badly-named-co/
views:
  - 8962
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - how to
  - naming conventions
  - sql server 2005
  - sql server 2008
  - tip
  - trick

---
Someone gives you a backup of a database, you restore it and the first thing you notice is that the column names have percent signs and underscores in them.
  
It would be easy to fix this if it was one table but in this case there are hundreds of tables.

The solution is to loop over information\_schema.columns, find all the columns that have those characters and then rename those columns by using the sp\_rename procedure. I will show you two ways to do this, one way that executes the code and one way that generates a script that you then can execute.

# Execute the code automatically

First create these two tables

<pre>CREATE TABLE Test ([col%1] VARCHAR(50),[col_2] VARCHAR(40))
GO

CREATE TABLE Test2 ([col%2] VARCHAR(50),[col_3] VARCHAR(40), [Col_%_%4] int)
GO</pre>

Now here is the code that will rename this in one shot. I have placed comments in the code to show you what the code does. If you want to know how sp_rename works visit the Books On Line link here: http://msdn.microsoft.com/en-us/library/ms188351.aspx

<pre>--Grab the table and columns that we need and store it in a temp table
SELECT IDENTITY(INT,1,1) AS id ,column_name,table_name, table_schema
INTO #LOOP
FROM information_schema.columns
WHERE column_name LIKE '%[%]%' --need to use brackets to escape %
OR column_name LIKE '%[_]%'    --need to use brackets to escape _

--set up loop variables
DECLARE @maxID INT, @loopid INT
SELECT @loopid =1  --start of loop
SELECT @maxID = MAX(id) FROM #LOOP --end of loop


--setup table and column name variables
DECLARE  @columnName VARCHAR(100)
DECLARE  @TableColumnNAme VARCHAR(100)

--loop until the end
WHILE @loopid &lt;= @maxID
BEGIN

--populate variables and take % and _ out from the column name
SELECT @TableColumnNAme = QUOTENAME(TABLE_SCHEMA) + '.' + QUOTENAME(TABLE_NAME) + '.' + QUOTENAME(COLUMN_NAME),
@columnName = REPLACE(REPLACE(COLUMN_NAME,'%',''),'_','')
FROM #LOOP WHERE id = @loopid


--rename the column
EXEC sp_rename @TableColumnNAme, @columnName, 'COLUMN';

--increment loop
SET @loopid = @loopid + 1
END

DROP TABLE #LOOP</pre>

Run a simple select statement to verify that the columns have been renamed

<pre>SELECT * FROM Test2</pre>

As you can see, there are no more underscores and percent signs in the column names.

# Generate a T-SQL Script

Here is the other way of doing the same thing. First drop and create the tables again

<pre>DROP TABLE Test,Test2

CREATE TABLE Test ([col%1] VARCHAR(50),[col_2] VARCHAR(40))
GO

CREATE TABLE Test2 ([col%2] VARCHAR(50),[col_3] VARCHAR(40), [Col_%_%4] int)
GO</pre>

Now hit CTRL + T to display the output in text, run the query below

<pre>SELECT		'EXEC sp_rename ''' 
			+ QUOTENAME(TABLE_SCHEMA) + '.' 
			+ QUOTENAME(TABLE_NAME) + '.' 
			+ QUOTENAME(COLUMN_NAME) + ''', ''' 
			+ REPLACE(REPLACE(COLUMN_NAME, '%', ''), '_', '') + ''', ''COLUMN'' ' 
			+ CHAR(13) + CHAR(10) + 'GO'  + CHAR(13) + CHAR(10)  
FROM INFORMATION_SCHEMA.COLUMNS 
WHERE column_name LIKE '%[%]%'
OR column_name LIKE '%[_]%'</pre>

The output from that query is the following T-SQL

<pre>EXEC sp_rename '[dbo].[Test].[col%1]', 'col1', 'COLUMN' 
GO
EXEC sp_rename '[dbo].[Test2].[col%2]', 'col2', 'COLUMN' 
GO
EXEC sp_rename '[dbo].[Test2].[Col_%_%4]', 'Col4', 'COLUMN' 
GO
EXEC sp_rename '[dbo].[Test].[col_2]', 'col2', 'COLUMN' 
GO
EXEC sp_rename '[dbo].[Test2].[col_3]', 'col3', 'COLUMN' 
GO</pre>

You can run that SQL and then you can run the same query against the table to see that the columns names don&#8217;t have those unwanted characters anymore

<pre>select * from Test2</pre>

As you can see, there are no more underscores and percent signs in the column names.

There you have it, two ways to do the same thing. Which way do you prefer, the first or the second?

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22