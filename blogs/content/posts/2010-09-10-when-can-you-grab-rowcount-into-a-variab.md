---
title: When should you store @@ROWCOUNT into a variable?
author: SQLDenis
type: post
date: 2010-09-10T11:53:04+00:00
ID: 887
url: /index.php/datamgmt/dbprogramming/when-can-you-grab-rowcount-into-a-variab/
views:
  - 20553
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - rowcount
  - error
  - gotcha
  - how to
  - sql server 2005
  - sql server 2008
  - tip

---
There was a question I answered the other day where [someone complained that the rowcount was always 0][1]. Below is a simplified version of the query, can you tell why @SomeCount will be 0?

```sql
declare @SomeCount int

select 1
union all
select 2

print '1'
select @SomeCount = @@rowcount 

select @SomeCount 
```

The value that the @SomeCount parameter returns will be 0 because print resets the value of @@ROWCOUNT to 0

Let's take a look at another example, but instead of using print we will use a _SET @param = value_ statement. Can you guess what @SomeCount will return in the select statement?

```sql
DECLARE @SomeCount INT
 
SELECT 1
UNION all
SELECT 2
 
SET  @SomeCount = 0
SELECT @SomeCount = @@ROWCOUNT
 
SELECT @SomeCount
```

The value that the @SomeCount parameter returns will be 1 because the _SET @param = value_ always sets the @@ROWCOUNT value to 1. Here is what Books On Line has on statements that make a simple assignment 

> Statements that make a simple assignment always set the @@ROWCOUNT value to 1. No rows are sent to the client. Examples of these statements are: SET @local_variable, RETURN, READTEXT, and select without query statements such as SELECT GETDATE() or SELECT 'Generic Text'.

Let's look at another example, what do you think will be returned in the query below

```sql
DECLARE @SomeCount INT, @SomeOtherCount int
 
 
SELECT  @SomeOtherCount = number 
FROM master..spt_values
SELECT @SomeCount = @@ROWCOUNT
 
SELECT @SomeCount
```

On my machine it returns the value 2506, this is because SQL Server reads all the rows from the master..spt_values table

Books On Line explanation on that one is the following

> Statements that make an assignment in a query or use RETURN in a query set the @@ROWCOUNT value to the number of rows affected or read by the query, for example: SELECT @local_variable = c1 FROM t1. 

Let's take a look at another rather silly example

```sql
DECLARE @SomeCount INT
 
SELECT 1
UNION all
SELECT 2
 
if 1=1
SELECT @SomeCount = @@ROWCOUNT
 
SELECT @SomeCount
```

As you can see the IF statement also resets the @@ROWCOUNT and you get back 0

## So when should you store @@ROWCOUNT into a variable?

If you want to store the rows that were affected by A DML statement then you need to grab @@ROWCOUNT immediately after the DML statement. There can't be any code between the DML statement and your code that stores @@ROWCOUNT into a variable.

So instead of this

```sql
declare @SomeCount int

select 1
union all
select 2

print '1'
select @SomeCount = @@rowcount 

select @SomeCount 
```

You do this, move the print until after you populate your variable with the @@ROWCOUNT value

```sql
declare @SomeCount int

select 1
union all
select 2
-- no other code beyween the DML statement and populating @SomeCount 
-- with the @@rowcount value
select @SomeCount = @@rowcount 
print '1'


select @SomeCount 
```

To learn more about @@ROWCOUNT visit Books On Line: http://msdn.microsoft.com/en-us/library/ms187316.aspx

## What about @@error

I am just giving you a little more code here, the same also applies for @@ERROR, so if you have code that needs to store both @@ERROR and @@ROWCOUNT then do it in one assignment.

There are 3 versions of the same query below, all of them will terminate with an error.
  
First let's look at code that first grabs the rowcount and then the error

```sql
declare @SomeCount int, @Error int

select 1
union all
select 2/0


select @SomeCount = @@rowcount 
select  @Error = @@error


select @SomeCount as TheRowcount, @Error as TheErrorCount
```

<pre>TheRowcount	TheErrorCount
0	        0</pre>

As you can see both variables are 0, TheRowcount is 0 because the query terminated and no rows were returned, TheErrorCount is 0 because the statement that assigned @SomeCount was successful 

Here is another example, all we did was reversed the assignment of @SomeCount and @Error

```sql
declare @SomeCount int, @Error int

select 1
union all
select 2/0

select  @Error = @@error
select @SomeCount = @@rowcount 

select @SomeCount as TheRowcount, @Error as TheErrorCount
```

<pre>TheRowcount	TheErrorCount
1	        8134</pre>

In this case TheRowcount is 1 because the statement that assigned @Error reset @@ROWCOUNT to 0

Finally we will look at what happens when you assign both variables with one statement

```sql
declare @SomeCount int, @Error int

select 1
union all
select 2/0

select  @Error = @@error, @SomeCount = @@rowcount 

select @SomeCount as TheRowcount, @Error as TheErrorCount
```

<pre>TheRowcount	TheErrorCount
0	        8134
</pre>

This is correct, TheErrorCount returns the error number and TheRowcount returns 0 because the query blew up

To learn more about @@ERROR visit Books On Line here: http://msdn.microsoft.com/en-us/library/ms188790.aspx

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://stackoverflow.com/questions/3575860/sql-why-cant-i-get-the-rowcount-value
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22