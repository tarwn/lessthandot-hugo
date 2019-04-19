---
title: What is deferred name resolution and why do you need to care?
author: SQLDenis
type: post
date: 2008-09-08T12:23:59+00:00
ID: 132
url: /index.php/datamgmt/datadesign/what-is-deferred-name-resolution-and-why/
views:
  - 15134
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - gotcha
  - howto
  - sql server
  - t-sql
  - tip
  - trick

---
So I posted [a teaser in the puzzles forum][1]. Without running this, try to guess what will happen?

```sql
DECLARE @x INT
 
SET @x = 1
 
IF (@x = 0)
BEGIN
    SELECT 1 AS VALUE INTO #temptable
END
ELSE
BEGIN
   SELECT 2 AS VALUE INTO #temptable
END
 
SELECT * FROM #temptable --what does this return
```

This is the error you get
  
Server: Msg 2714, Level 16, State 1, Line 12
  
There is already an object named '#temptable' in the database.

You can do something like this to get around the issue with the temp table

```sql
DECLARE @x INT
 
SET @x = 1
 
CREATE TABLE #temptable (VALUE int)
IF (@x = 0)
BEGIN
    INSERT #temptable
    SELECT 1 
END
ELSE
BEGIN
    INSERT #temptable
    SELECT 2
END
 
SELECT * FROM #temptable --what does this return
```

So what is thing called Deferred Name Resolution? Here is what is explained in Books On Line

> When a stored procedure is created, the statements in the procedure are parsed for syntactical accuracy. If a syntactical error is encountered in the procedure definition, an error is returned and the stored procedure is not created. If the statements are syntactically correct, the text of the stored procedure is stored in the syscomments system table.
> 
> When a stored procedure is executed for the first time, the query processor reads the text of the stored procedure from the syscomments system table of the procedure and checks that the names of the objects used by the procedure are present. This process is called deferred name resolution because objects referenced by the stored procedure need not exist when the stored procedure is created, but only when it is executed.
> 
> In the resolution stage, Microsoft SQL Server 2000 also performs other validation activities (for example, checking the compatibility of a column data type with variables). If the objects referenced by the stored procedure are missing when the stored procedure is executed, the stored procedure stops executing when it gets to the statement that references the missing object. In this case, or if other errors are found in the resolution stage, an error is returned.

So what is happening is that beginning with SQL server 7 deferred name resolution was enabled for real tables but not for temporary tables. If you change the code to use a real table instead of a temporary table you won't have any problem
  
Run this to see what I mean

```sql
DECLARE @x INT
 
SET @x = 1
 
IF (@x = 0)
BEGIN
    SELECT 1 AS VALUE INTO temptable
END
ELSE
BEGIN
   SELECT 2 AS VALUE INTO temptable
END
 
SELECT * FROM temptable --what does this return
```

What about variables? Let's try it out, run this

```sql
DECLARE @x INT
 
SET @x = 1
 
IF (@x = 0)
BEGIN
    DECLARE @i INT
    SELECT @i = 5
END
ELSE
BEGIN
   DECLARE @i INT
   SELECT @i = 6
END
 
SELECT @i
```

And you get the follwing error
  
Server: Msg 134, Level 15, State 1, Line 13
  
The variable name '@i' has already been declared. Variable names must be unique within a query batch or stored procedure.

Now why do you need to care about deferred name resolution? Let's take another example from a blogpost I made a while back: [Do you depend on sp_depends (no pun intended)][2] 

First create this proc

```sql
CREATE PROC SomeTestProc
AS
SELECT dbo.somefuction(1)
GO
```

now create this function

```sql
CREATE FUNCTION somefuction(@id int)
RETURNS int
AS
BEGIN
SELECT @id = 1
RETURN @id
END
Go
```

now run this

```sql
sp_depends 'somefuction'
```

result: Object does not reference any object, and no objects reference it.

Most people will not create a proc before they have created the function. So when does this behavior rear its ugly head? When you script out all the objects in a database, if the function or any objects referenced by an object are created after the object that references them then sp_depends won't be 100% correct

SQL Server 2005 makes it pretty easy to do it yourself

```sql
SELECT specific_name,* 
FROM information_schema.routines 
WHERE object_definition(object_id(specific_name)) LIKE '%somefuction%' 
AND routine_type = 'procedure'
```


 [1]: http://forum.ltd.local/viewtopic.php?f=102&t=2829
 [2]: http://sqlblog.com/blogs/denis_gobo/archive/2008/05/06/6653.aspx