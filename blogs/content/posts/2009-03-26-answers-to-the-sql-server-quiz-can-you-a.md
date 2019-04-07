---
title: 'Answers To The SQL Server Quiz: Can You Answer All These Post'
author: SQLDenis
type: post
date: 2009-03-26T12:14:03+00:00
url: /index.php/datamgmt/datadesign/answers-to-the-sql-server-quiz-can-you-a/
views:
  - 10937
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - quiz
  - sql
  - sql server 2000
  - sql server 2005
  - sql server 2008

---
Yesterday I wrote the [SQL Server Quiz, Can You Answer All These?][1] post and asked 10 question. Today I will give you the answes

**1) Name three differences between primary keys and unique constraints**
  
A primary key cannot have any null values and a unique constraint can have one null value
  
A primary key is by default clustered and a unique constraint is not
  
A table can only have one primary key but can have more than one unique constraint

**2) If your database is in simple recovery model and you run code that looks like this**

<pre>BULK INSERT Northwind.dbo.[Order Details]
   FROM 'f:orderslineitem.tbl'
   WITH 
      (
         FIELDTERMINATOR = '|',
         ROWTERMINATOR = '|n'
      )</pre>

Will this be minimally logged?

There are some additional things you need to do before a bulk insert is minimally logged, one of them would be that you would need to lock the table.

Here is an example

<pre>BULK INSERT Northwind.dbo.[Order Details]
   FROM 'f:orderslineitem.tbl'
   WITH 
      (
         FIELDTERMINATOR = '|',
         ROWTERMINATOR = '|n',
	 TABLOCK
      )</pre>

There is actually more that is required, here is what books on line specifies about it

A minimally logged bulk copy can be performed if all of these conditions are met:

  * The recovery model is simple or bulk-logged.
  * The target table is not being replicated.
  * The target table does not have any triggers.
  * The target table has either 0 rows or no indexes.
  * The TABLOCK hint is specified.

**3) How many flaws/worst practices are in this piece of code**

<pre>select * 
from SomeTable
Where left(SomeColumn,1) ='A'

print 'query executed'
select @@rowcount as 'Rows returned'</pre>

There are a couple of things that stand out
  
You should never use * but only list the columns you really want, see also here: [Don’t use * but list the columns][2]
  
There is a missing qualifier in the table name
  
This where clause is not sargable instead of left(SomeColumn,1) =&#8217;A&#8217; do this SomeColumn like &#8216;A%&#8217;. see also here: [Functions on left side of the operator][3]
  
@@ROWCOUNT should be selected right after the query because the print will make it have a value of 0; any statement will affect @@ROWCOUNT so you should always dump it into a variable right after you do a DML statement. The same that applies to @@ROWCOUNT also applies to @@ERROR. If you do need to grab both @@ERROR and @@rowcount then do it on the same line. SELECT @MyErr =@@ERROR, @MyCount = @@ROWCOUNT 

**4) When we use Try and Catch will the following tran be commited?**

<pre>BEGIN TRANSACTION TranA
    BEGIN TRY
     DECLARE  @cond INT;
     SET @cond =  'A';
    END TRY
    BEGIN CATCH
     PRINT 'Inside catch'
    END CATCH;
    COMMIT TRAN TranA</pre>

No this will result in a doomed transaction and you will see the following message: _Msg 3930, Level 16, State 1, Line 15 The current transaction cannot be committed and cannot support operations that write to the log file. Roll back the transaction. Server: Msg 3998, Level 16, State 1, Line 1 Uncommittable transaction is detected at the end of the batch. The transaction is rolled back._

Before I show you what you can do first run this

<pre>BEGIN TRANSACTION TranA
    BEGIN TRY
     DECLARE  @cond INT;
     SET @cond =  1/0;
    END TRY
    BEGIN CATCH
     PRINT 'Inside catch'
    END CATCH;
    COMMIT TRAN TranA</pre>

See that was no problem at all, the first code blew up because it is a non trapable error. You can use XACT_STATE() to see what state the transaction is in

<pre>BEGIN TRANSACTION TranA
    BEGIN TRY
     DECLARE  @cond INT;
     SET @cond = 'A';
    END TRY
    BEGIN CATCH
     PRINT ERROR_MESSAGE();
    END CATCH;
    IF XACT_STATE() =0
    BEGIN
     COMMIT TRAN TranA
    END
    ELSE
    BEGIN
     ROLLBACK TRAN TranA
    END</pre>

To learn more about errors and transactions I highly recommend these two links by Erland Sommarskog: [Implementing Error Handling with Stored Procedures][4] and [Error Handling in SQL Server – a Background][5].

**5)Take a look at the code below, what will the last select return?**

<pre>declare @SQL varchar(100)
declare @Val varchar(10)

select @SQL ='The value this item is..'

select @SQL + isnull(@Val,' currently not available')</pre>

Running that code will return the following: The value this item is.. currently. This is because isnull looks at @Val which is varchar(10) and chops off everything after 10 characters. If you use coalesce then you don&#8217;t have this problem, run the following

<pre>declare @SQL varchar(100)
declare @Val varchar(10)

select @SQL ='The value this item is..'

select @SQL + coalesce(@Val,' currently not available')</pre>

And now this is returned The value this item is.. currently not available

**6)What will the returned when you run the following query?**

<pre>select 3/2</pre>

So running that returns 1, surprised? Don&#8217;t be the result of division with two integers is an integer, this is also known as [integer math][6]

Here is how you can fix it by doing explicit and implicit conversions

<pre>--Implicit
    SELECT 3/(2*1.0)
    --Explicit
    SELECT CONVERT(DECIMAL(18,4),3)/2</pre>

**7)How many rows will the select query return from the table with 3 rows**

<pre>CREATE TABLE #testnulls (ID INT)
INSERT INTO #testnulls VALUES (1)
INSERT INTO #testnulls VALUES (2)
INSERT INTO #testnulls VALUES (null)

select * from #testnulls
where id <> 1</pre>

The answer is one row, the reason for that is that a null is not equal to anything not even another null

Run this to see what I mean

<pre>if null = null
print 'yes null = null'
else
print 'no null = null'

if null is null
print 'yes null is null'
else
print 'no null is null'</pre>

The following will be printed
  
no null = null
  
yes null is null

To check for null you would use IS NULL and IS NOT NULL or NOT IS NULL, you would not use = NULL or <> NULL

**8)If you run the code below what will the len function return, can you also answer why?**

<pre>declare @v varchar(max)
select @v =replicate('a',20000)

select len(@v)</pre>

8000 will be returned because &#8216;a&#8217; is a varchar which goes up to 8000 max. Here is one way to get around it

<pre>declare @v varchar(max)
select @v =replicate(convert(varchar(max),'a'),20000)

select len(@v)</pre>

**9) If you have the following table**

<pre>CREATE TABLE #testnulls2 (ID INT)
INSERT INTO #testnulls2 VALUES (1)
INSERT INTO #testnulls2 VALUES (2)
INSERT INTO #testnulls2 VALUES (null)</pre>

what will the query below return?

<pre>select count(*), count(id)
from #testnulls2</pre>

This will return 3 and 2. this is because count(*) counts all the columns and count(id) will only count the non null values in a column

**10)If you have the following two tables**

<pre>CREATE TABLE TestOne (id INT IDENTITY,SomeDate DATETIME)
CREATE TABLE TestTwo (id INT IDENTITY,TestOneID INT,SomeDate DATETIME)
 
    --Let's insert 4 rows into the table
    INSERT TestOne VALUES(GETDATE())
    INSERT TestOne VALUES(GETDATE())
    INSERT TestOne VALUES(GETDATE())
    INSERT TestOne VALUES(GETDATE())</pre>

If table TestOne now has the following trigger added to it

<pre>CREATE TRIGGER trTestOne ON [dbo].[TestOne]
    FOR INSERT
    AS
    DECLARE @CreditUserID INT
 
    SELECT @CreditUserID = (SELECT ID FROM Inserted)
 
    INSERT TestTwo VALUES(@CreditUserID,GETDATE())
    GO</pre>

What will be the value that the @@identity function returns after a new insert into the TestOne table?

<pre>INSERT TestOne VALUES(GETDATE())
select @@identity</pre>

You will get back the value 1 and not 5. This is because @@IDENTITY doesn&#8217;t care about scope and returns the last identity value from all the statements, which in this case is from the code within the trigger trTestOne. So the bottom line is this: Always use SCOPE_IDENTITY() unless you DO need the last identity value regradless of scope (for example you need to know the identity from the table insert inside the trigger)
  
Run this now and you will see both values and you can see that they are indeed different

<pre>INSERT TestOne VALUES(GETDATE())
select scope_identity(), @@identity</pre>

So that is it for all 10 questions

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][7] forum or our [Microsoft SQL Server Admin][8] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/sql-server-quiz-can-you-answer-all-these
 [2]: http://wiki.ltd.local/index.php/SQL_Server_Programming_Hacks_-_Don%27t_Use_%28select_%2A%29%2C_but_List_Columns
 [3]: http://wiki.ltd.local/index.php/SQL_Server_Programming_Hacks_-_No_Functions_on_Left_Side_of_Operator
 [4]: http://www.sommarskog.se/error-handling-II.html
 [5]: http://www.sommarskog.se/error-handling-I.html
 [6]: http://wiki.ltd.local/index.php/Integer_math
 [7]: http://forum.lessthandot.com/viewforum.php?f=17
 [8]: http://forum.lessthandot.com/viewforum.php?f=22