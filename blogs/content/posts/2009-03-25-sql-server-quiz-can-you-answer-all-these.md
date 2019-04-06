---
title: SQL Server Quiz, Can You Answer All These?
author: SQLDenis
type: post
date: 2009-03-25T12:44:12+00:00
url: /index.php/datamgmt/datadesign/sql-server-quiz-can-you-answer-all-these/
views:
  - 27286
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
Here is a quick SQL Server quiz. Do you know the answer to all of these question? I will give the answers in a blog post tomorrow

1) Name three differences between primary keys and unique constraints

2) If your database is in simple recovery model and you run code that looks like this

<pre>BULK INSERT Northwind.dbo.[Order Details]
   FROM 'f:orderslineitem.tbl'
   WITH 
      (
         FIELDTERMINATOR = '|',
         ROWTERMINATOR = '|n'
      )</pre>

Will this be minimally logged?

3) How many flaws/worst practices are in this piece of code

<pre>select * 
from SomeTable
Where left(SomeColumn,1) ='A'

print 'query executed'
select @@rowcount as 'Rows returned'</pre>

4) When we use Try and Catch will the following tran be commited?

<pre>BEGIN TRANSACTION TranA
    BEGIN TRY
     DECLARE  @cond INT;
     SET @cond =  'A';
    END TRY
    BEGIN CATCH
     PRINT 'Inside catch'
    END CATCH;
    COMMIT TRAN TranA</pre>

5)Take a look at the code below, what will the last select return?

<pre>declare @SQL varchar(100)
declare @Val varchar(10)

select @SQL ='The value this item is..'

select @SQL + isnull(@Val,' currently not available')</pre>

6)What will the returned when you run the following query?

<pre>select 3/2</pre>

7)How many rows will the select query return from the table with 3 rows

<pre>CREATE TABLE #testnulls (ID INT)
INSERT INTO #testnulls VALUES (1)
INSERT INTO #testnulls VALUES (2)
INSERT INTO #testnulls VALUES (null)

select * from #testnulls
where id &lt;&gt; 1</pre>

8)If you run the code below what will the len function return, can you also answer why?

<pre>declare @v varchar(max)
select @v =replicate('a',20000)

select len(@v)</pre>

9) If you have the following table

<pre>CREATE TABLE #testnulls2 (ID INT)
INSERT INTO #testnulls2 VALUES (1)
INSERT INTO #testnulls2 VALUES (2)
INSERT INTO #testnulls2 VALUES (null)</pre>

what will the query below return?

<pre>select count(*), count(id)
from #testnulls2</pre>

10)If you have the following two tables

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

So those are the 10 questions for this little quiz. I will post the answers/explanations to these tomorrow, if you are still bored here is a bunch of stuff that you should be familiar with as a SQL Server developer

Update ** I created the answers to these questions and you can find them here: [Answers To The SQL Server Quiz: Can You Answer All These Post][1]

_What is normalization
  
What is the fastest way to empty a table
  
what is a deadlock
  
Can you give an example of creating a deadlock
  
How do you detect deadlocks
  
What is an audit trail
  
what is an identity column
  
How many bytes can you fit in a row, do you know why
  
What is a clustered index
  
How many clustered indexes per table
  
How many nonclustered indexes per table
  
what is an execution plan
  
Between index scan, index seek and table scan; which one is fastest and which one is slowest
  
How do you return a value from a proc
  
How do you return a varchar value from a proc
  
If I have a column that will only have values between 1 and 250 what data type should I use
  
How do you enforce that only values between 1 and 10 are allowed in a column
  
How to check for a valid date
  
Which date format is the only safe one to use when passing dates as strings
  
How do you suppress rows affected messages when executing an insert statement
  
Can you name the 4 isolation levels in SQL Server 2000
  
How would you select all last names that start with S
  
How would you select all rows where the date is 20061127
  
What is horizontal partitioning
  
What does schemabinding do
  
How do you test for nulls
  
Name some differences between isnull and coalesce
  
What is a temp table
  
what is the difference between a local and global temporary table
  
If you create a local temp table and then call a proc is the temp table available inside the proc
  
What is referential integrity
  
what is the fastest way to populate a table (performance wise)
  
using the method above what can you do to make it even faster
  
What data type should you use to store monetary values
  
What is a cascade delete
  
Name a couple of types of joins
  
What is a SQL injection
  
What is parameter sniffing
  
Name 2 differences between a primary key and UNIQUE Constraints
  
How do you ensure that SQL server will use an index
  
What does option fast (10) do
  
What is the difference between union and union all
  
What does trace flag 1204 do_

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/answers-to-the-sql-server-quiz-can-you-a
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22