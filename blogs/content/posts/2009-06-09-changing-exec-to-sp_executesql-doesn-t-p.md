---
title: Changing exec to sp_executesql doesn’t provide any benefit if you are not using parameters correctly
author: SQLDenis
type: post
date: 2009-06-09T18:36:42+00:00
ID: 462
excerpt: |
  Changing exec to sp_executesql doesn't provide any benefit if you are not using parameters correctly
  
  
  
  I was looking through some code recently and noticed all these sp_executesql calls which did not use parameters correctly.
  A typical SQL stateme&hellip;
url: /index.php/datamgmt/datadesign/changing-exec-to-sp_executesql-doesn-t-p/
views:
  - 33546
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - dynamic sql
  - exec
  - sp_executesql
  - sql

---
Changing exec to sp_executesql doesn't provide any benefit if you are not using parameters correctly

I was looking through some code recently and noticed all these sp_executesql calls which did not use parameters correctly.
  
A typical SQL statement would look like this

sql
declare @Col2 smallint
declare @Col1 int

select @Col2 = 4,@Col1 = 5

declare @SQL nvarchar(1000)
select @SQL = 'select * from test
where Col2 = ' + convert(varchar(10),@Col2)+ '
and Col1 = ' + convert(varchar(10),@Col1)



exec sp_executesql @SQL
```

What that code does is it builds a SQL statement and executes it. The problem is that when you do something like that the query plan will not be reused when you change the values of @Col2 and @Col1. When a new plan is generated everytime your values change you will bloat SQL Server's procedure cache and less memory will be available for data.
  
Below is some code to demonstrate what I mean, I have tested this code on SQL Server 2008 only!!

First create this table

sql
create table dbo.test (Col1 int primary key,
Col2 smallint not null,
SomeDate datetime default getdate(),
SomeValue char(10) default 'ABCDEFG')
GO
```

Insert a bunch of rows

sql
insert dbo.test(Col1,Col2)
select number+ 1,number from master..spt_values
where type = 'P'
order by number
```

Now let's see what we inserted

sql
select * from dbo.test
```

(results abridged)
  
Col1 Col2 SomeDate SomeValue
  
1 0 2009-06-09 11:50:04.327 ABCDEFG
  
2 1 2009-06-09 11:50:04.327 ABCDEFG
  
3 2 2009-06-09 11:50:04.327 ABCDEFG
  
4 3 2009-06-09 11:50:04.327 ABCDEFG
  
5 4 2009-06-09 11:50:04.327 ABCDEFG
  
6 5 2009-06-09 11:50:04.327 ABCDEFG
  
7 6 2009-06-09 11:50:04.327 ABCDEFG
  
…..
  
…..
  
…..
  
2047 2046 2009-06-09 11:50:04.327 ABCDEFG
  
2048 2047 2009-06-09 11:50:04.327 ABCDEFG 

First let's clear our procedure cache

sql
dbcc freeproccache
```

run these 2 queries 5 times

sql
select * from dbo.test
where Col2 = 3
and Col1 = 4
go

select * from dbo.test
where Col2 = 4
and Col1 = 5
go
```

Now run the following query to see how many plans we have.

sql
select q.text,cp.usecounts,cp.objtype,p.*,
q.*,
cp.plan_handle
from
sys.dm_exec_cached_plans cp
cross apply sys.dm_exec_query_plan(cp.plan_handle) p
cross apply sys.dm_exec_sql_text(cp.plan_handle) as q
where
cp.cacheobjtype = 'Compiled Plan' and q.text  like '%dbo.test%'
and q.text  not like '%sys.dm_exec_cached_plans %'
```

As you can see we have 2 plans and each was used 5 times. So for each change in the value a new plan gets generated

Let's clear the cache again

sql
dbcc freeproccache
```

Using dynamic SQL with changing parameters also creates a new plan every time you change the values of the parameters.
  
Run the following block of code 5 times

sql
declare @Col2 smallint
declare @Col1 int

select @Col2 = 11,@Col1 = 12

declare @SQL varchar(1000)
select @SQL = 'select * from dbo.test
where Col2 = ' + convert(varchar(10),@Col2)+ '
and Col1 = ' + convert(varchar(10),@Col1)


exec (@SQL)

go

declare @Col2 smallint
declare @Col1 int

select @Col2 = 12,@Col1 = 13

declare @SQL varchar(1000)
select @SQL = 'select * from dbo.test
where Col2 = ' + convert(varchar(10),@Col2)+ '
and Col1 = ' + convert(varchar(10),@Col1)


exec (@SQL)
go
```

Now let's see how many plans we have

sql
select q.text,cp.usecounts,cp.objtype,p.*,
q.*,
cp.plan_handle
from
sys.dm_exec_cached_plans cp
cross apply sys.dm_exec_query_plan(cp.plan_handle) p
cross apply sys.dm_exec_sql_text(cp.plan_handle) as q
where
cp.cacheobjtype = 'Compiled Plan' and q.text  like '%dbo.test%'
and q.text  not like '%sys.dm_exec_cached_plans %'
```

As you can see we have 2 plans with a count of 5 for each.

Now let's convert that query to use sp_executesql instead of exec

Run the query below

sql
declare @Col2 smallint
declare @Col1 int

select @Col2 = 3,@Col1 = 4

declare @SQL varchar(1000)
select @SQL = 'select * from dbo.test
where Col2 = ' + convert(varchar(10),@Col2)+ '
and Col1 = ' + convert(varchar(10),@Col1)



exec sp_executesql @SQL
```

And you get the following message
  
**Server: Msg 214, Level 16, State 2, Procedure sp_executesql, Line 1
  
Procedure expects parameter &#8216;@statement' of type &#8216;ntext/nchar/nvarchar'.**

This is because sp_executesql expects nvarchar and not varchar

Below is the correct query(but it is not correctly parameterized). First clear the cache again

sql
dbcc freeproccache
```

Now run the following queries 5 times each

sql
declare @Col2 smallint
declare @Col1 int

select @Col2 = 23,@Col1 = 24

declare @SQL nvarchar(1000)
select @SQL = 'select * from dbo.test
where Col2 = ' + convert(varchar(10),@Col2)+ '
and Col1 = ' + convert(varchar(10),@Col1)


exec sp_executesql @SQL
Go


declare @Col2 smallint
declare @Col1 int

select @Col2 = 22,@Col1 = 23

declare @SQL nvarchar(1000)
select @SQL = 'select * from dbo.test
where Col2 = ' + convert(varchar(10),@Col2)+ '
and Col1 = ' + convert(varchar(10),@Col1)



exec sp_executesql @SQL
GO
```
Now check again for the plans

sql
select q.text,cp.usecounts,cp.objtype,p.*,
q.*,
cp.plan_handle
from
sys.dm_exec_cached_plans cp
cross apply sys.dm_exec_query_plan(cp.plan_handle) p
cross apply sys.dm_exec_sql_text(cp.plan_handle) as q
where
cp.cacheobjtype = 'Compiled Plan' and q.text  like '%dbo.test%'
and q.text  not like '%sys.dm_exec_cached_plans %'
```

As you can see we have 2 plans with a count of 5 for each. This is because we didn't use sp_executesql correctly and the engine couldn't reuse the plan. Here is what Books On Line has to say

> sp_executesql can be used instead of stored procedures to execute a Transact-SQL statement a number of times when the change in parameter values to the statement is the only variation. Because the Transact-SQL statement itself remains constant and only the parameter values change, the Microsoft® SQL Server™ query optimizer is likely to reuse the execution plan it generates for the first execution.

Below is the query which is correctly parameterized. As you can see we have variables inside the string and at execution time we pass values by means of other variables to it.

First clear the cache again

sql
dbcc freeproccache
```

Now run the following queries 5 times each

sql
declare @Col2 smallint, @Col1 int
select @Col2 = 3,@Col1 = 4


declare @SQL nvarchar(1000)
select @SQL = 'select * from dbo.test
where Col2 = @InnerCol2 and Col1 = @InnerCol1' 

declare @ParmDefinition nvarchar(500)
SET @ParmDefinition = N'@InnerCol2 smallint ,@InnerCol1 int'



exec sp_executesql 	@SQL,@ParmDefinition,
			@InnerCol2 	= @Col2,
			@InnerCol1 	= @Col1
go


declare @Col2 smallint, @Col1 int
select @Col2 = 3,@Col1 = 4


declare @SQL nvarchar(1000)
select @SQL = 'select * from dbo.test
where Col2 = @InnerCol2 and Col1 = @InnerCol1' 

declare @ParmDefinition nvarchar(500)
SET @ParmDefinition = N'@InnerCol2 smallint ,@InnerCol1 int'



exec sp_executesql 	@SQL,@ParmDefinition,
			@InnerCol2 	= @Col2,
			@InnerCol1 	= @Col1

go
```

Check the plans again

sql
select q.text,cp.usecounts,cp.objtype,p.*,
q.*,
cp.plan_handle
from
sys.dm_exec_cached_plans cp
cross apply sys.dm_exec_query_plan(cp.plan_handle) p
cross apply sys.dm_exec_sql_text(cp.plan_handle) as q
where
cp.cacheobjtype = 'Compiled Plan' and q.text  like '%dbo.test%'
and q.text  not like '%sys.dm_exec_cached_plans %'
```

And you will see that we have only one plan with a count of 10

Instead of running the query like we did before we can also do the following. We only have to declare everything once and then we just need to change the values of the parameters before executing

First clear the cache yet again

sql
dbcc freeproccache
```

Here is the rewritten query, execute it 5 times

sql
declare @Col2 smallint, @Col1 int
select @Col2 = 3,@Col1 = 4


declare @SQL nvarchar(1000)
select @SQL = 'select * from dbo.test
where Col2 = @InnerCol2 and Col1 = @InnerCol1' 

declare @ParmDefinition nvarchar(500)
SET @ParmDefinition = N'@InnerCol2 smallint ,@InnerCol1 int'



exec sp_executesql 	@SQL,@ParmDefinition,
			@InnerCol2 	= @Col2,
			@InnerCol1 	= @Col1

--change param values and run the same query
select @Col2 = 2,@Col1 = 3
exec sp_executesql 	@SQL,@ParmDefinition,
			@InnerCol2 	= @Col2,
			@InnerCol1 	= @Col1

go 
```

And we will check the plans yet again

sql
select q.text,cp.usecounts,cp.objtype,p.*,
q.*,
cp.plan_handle
from
sys.dm_exec_cached_plans cp
cross apply sys.dm_exec_query_plan(cp.plan_handle) p
cross apply sys.dm_exec_sql_text(cp.plan_handle) as q
where
cp.cacheobjtype = 'Compiled Plan' and q.text  like '%dbo.test%'
and q.text  not like '%sys.dm_exec_cached_plans %'
```

As you can see we still have a count of 10 and only one plan.

As you can see sp\_executesql can be beneficial for performance when used correctly. Using sp\_executesql will also give you some additional features you can't do with EXEC.

How would you get a count of rows in a table? with EXEC you need to use a temp table and populate that, with sp_executesql you can use an output variable

Take a look at the following queries

Here is the EXEC version

sql
--EXEC (SQL)
DECLARE @TableName VARCHAR(100),
@TableCount INT,
@SQL NVARCHAR(100)
 
 
CREATE TABLE #temp (Totalcount INT)
SELECT @TableName = 'test'
SELECT @SQL = 'Insert into #temp Select Count(*) from ' + @TableName
 
EXEC( @SQL)
 
SELECT @TableCount = Totalcount FROM #temp
 
SELECT @TableCount as TheCount
 
DROP TABLE #temp
GO
```

Here is the sp_executesql version

sql
--sp_executesql
DECLARE @TableName VARCHAR(100),
@TableCount INT,
@SQL NVARCHAR(100)
 
SELECT @TableName = 'Test'
SELECT @SQL = N'SELECT @InnerTableCount = COUNT(*) FROM ' + @TableName
 
EXEC SP_EXECUTESQL @SQL, N'@InnerTableCount INT OUTPUT', @TableCount OUTPUT
 
SELECT @TableCount
GO
```

There are more differences between EXEC and sp\_executesql, one of the more important one is that sp\_executesql can protect you from **SQL Injection**. I encourage you to read [The curse and blessings of dynamic SQL][1] to learn more stuff

I have written a follow up to this post that explains how to avoid conversions. Here is the link: [Avoid Conversions In Execution Plans By Using sp_executesql Instead of Exec][2]



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://www.sommarskog.se/dynamic_sql.html
 [2]: /index.php/DataMgmt/DataDesign/avoid-conversions-in-execution-plans-by
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22