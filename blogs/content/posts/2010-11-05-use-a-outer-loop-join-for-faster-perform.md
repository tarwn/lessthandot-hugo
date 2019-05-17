---
title: 'Use an  OUTER LOOP JOIN for faster performance when one of the tables is much smaller'
author: SQLDenis
type: post
date: 2010-11-05T11:45:58+00:00
ID: 934
excerpt: |
  A couple of days ago a coworker was having problems with an insert statement. This insert statement needed to insert data into a table if that data didn't already exists in the table he was inserting to.
  
  The table he was inserting to had about 20 mil&hellip;
url: /index.php/datamgmt/dbprogramming/use-a-outer-loop-join-for-faster-perform/
views:
  - 19741
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
A couple of days ago a coworker was having problems with an insert statement. This insert statement needed to insert data into a table if that data didn't already exists in the table he was inserting to.

The table he was inserting to had about 20 million rows and the table he was inserting from had about 100 thousand rows. The problem he had was that the left join that checked if the rows existed was nor performing very well. Since one table was much smaller compared to the other table I suggested a _left outer loop join_. Once he changed to the _left outer loop join_ the query was about 20 times faster because now it performed an index seek instead of an index scan.

Here is what Books On Line has to say about [Nested loop joins][1]

> A nested loops join is particularly effective if the outer input is small and the inner input is preindexed and large. In many small transactions, such as those affecting only a small set of rows, index nested loops joins are superior to both merge joins and hash joins. In large queries, however, nested loops joins are often not the optimal choice.

Of course it would be nice if we had some code that showed the difference, the code in this article will use the following 3 methods:

regular left join
  
not exists
  
left loop join

First we need to create some data, in order to do that we will create a small table and then use that in a cross join to populate the table which will hold 20 million rows.

This table will hold values from AAA until ZZZ

```sql
create table TestSomeValue (SomeChar char(3))
go

;with cte as(
select char(number) as charnum
from master..spt_values
where type = 'p'
and number between 65 and 90)

insert TestSomeValue
select c.charnum + c2.charnum + c3.charnum 
from cte c cross join cte c2
cross join cte c3
```

This will be our large table with 20212400 rows, this might run for a couple of minutes

```sql
create table TestLarge (SomeValue char(3), SomeDate datetime, SomeFlag char(1), SomeOtherValue varchar(100))
go



insert TestLarge
select *,1,NEWID() from TestSomeValue t
cross join (
select dateadd(dd,number,'20010101') as SomeDate
from master..spt_values
where type = 'p'
and number < 1150) x
```

This will be our small table and will have 115456 rows

```sql
create table TestSmall (SomeValue char(3), SomeDate datetime, SomeFlag char(1), SomeOtherValue varchar(100))
go

--insert 105456 rows that do not exist
insert TestSmall
select *,1,NEWID() from TestSomeValue t
cross join (
select dateadd(dd,number,'20010101') as SomeDate
from master..spt_values
where type = 'p'
and number between 1160 and 1165) x

--insert 10000 rows that do exist
insert TestSmall
select top 10000 * 
from TestLarge
```

Create a clustered index on both tables, this also might take a couple of minutes

```sql
create clustered index ix_TestSmall on TestSmall(SomeValue,SomeDate,SomeFlag,SomeOtherValue)
go


create clustered index ix_TestLarge on TestLarge(SomeValue,SomeDate,SomeFlag,SomeOtherValue)
go
```

Here we have the 3 versions of the same kind of query, a plain vanilla left join, a not exist and a left loop join

Include the actual execution plan and run these 3 queries

```sql
Select COUNT(*) -- 105456
from TestSmall t1
left join TestLarge t2 on t1.SomeValue = t2.SomeValue
and t1.SomeDate =t2.SomeDate
and t1.SomeFlag = t2.SomeFlag
and t1.SomeOtherValue = t2.SomeOtherValue
where t2.SomeValue is null

select count(*)  --105456
from TestSmall t1
where not exists (
select 1 from  TestLarge t2 where t1.SomeValue = t2.SomeValue
and t1.SomeDate =t2.SomeDate
and t1.SomeFlag = t2.SomeFlag
and t1.SomeOtherValue = t2.SomeOtherValue)

select COUNT(*) -- 105456
from TestSmall t1
left outer loop join TestLarge t2 on t1.SomeValue = t2.SomeValue
and t1.SomeDate =t2.SomeDate
and t1.SomeFlag = t2.SomeFlag
and t1.SomeOtherValue = t2.SomeOtherValue
where t2.SomeValue is null
```

Below is the execution plan generated when you run these 3 queries, click on the image for a larger version.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/ExecutionPlan.png" alt="Execution Plan" title="Execution Plan" width="979" height="521" />
</div>

As you can see the loop join doesn't look that impressive compared to the rest, however it is the only query that does a seek instead of a scan

What about the reads? Run the following query

```sql
set statistics io on
go
select COUNT(*) -- 105456
from TestSmall t1
left join TestLarge t2 on t1.SomeValue = t2.SomeValue
and t1.SomeDate =t2.SomeDate
and t1.SomeFlag = t2.SomeFlag
and t1.SomeOtherValue = t2.SomeOtherValue
where t2.SomeValue is null

select count(*)  --105456
from TestSmall t1
where not exists (
select 1 from  TestLarge t2 where t1.SomeValue = t2.SomeValue
and t1.SomeDate =t2.SomeDate
and t1.SomeFlag = t2.SomeFlag
and t1.SomeOtherValue = t2.SomeOtherValue)

select COUNT(*) -- 105456
from TestSmall t1
left outer loop join TestLarge t2 on t1.SomeValue = t2.SomeValue
and t1.SomeDate =t2.SomeDate
and t1.SomeFlag = t2.SomeFlag
and t1.SomeOtherValue = t2.SomeOtherValue
where t2.SomeValue is null

set statistics io off
go
```

Here is what is returned
  
**Left Join**
  
Table 'Worktable'. Scan count 0, logical reads 0, physical reads 0, read-ahead reads 0
  
Table 'TestLarge'. Scan count 1, logical reads 159177, physical reads 0, read-ahead reads 0
  
Table 'TestSmall'. Scan count 1, logical reads 913, physical reads 0, read-ahead reads 0

**Not Exist**
  
Table 'TestSmall'. Scan count 1, logical reads 913, physical reads 0, read-ahead reads 0
  
Table 'TestLarge'. Scan count 1, logical reads 159177, physical reads 0, read-ahead reads 0

**Left Loop Join**
  
Table 'TestLarge'. Scan count 115456, logical reads 492566, physical reads 0, read-ahead reads 0
  
Table 'TestSmall'. Scan count 3, logical reads 1006, physical reads 0, read-ahead reads 0
  
Table 'Worktable'. Scan count 0, logical reads 0, physical reads 0, read-ahead reads 0

Finally let's look at how long each query took

```sql
set statistics time on
go
select COUNT(*) -- 105456
from TestSmall t1
left join TestLarge t2 on t1.SomeValue = t2.SomeValue
and t1.SomeDate =t2.SomeDate
and t1.SomeFlag = t2.SomeFlag
and t1.SomeOtherValue = t2.SomeOtherValue
where t2.SomeValue is null

select count(*)  --105456
from TestSmall t1
where not exists (
select 1 from  TestLarge t2 where t1.SomeValue = t2.SomeValue
and t1.SomeDate =t2.SomeDate
and t1.SomeFlag = t2.SomeFlag
and t1.SomeOtherValue = t2.SomeOtherValue)

select COUNT(*) -- 105456
from TestSmall t1
left outer loop join TestLarge t2 on t1.SomeValue = t2.SomeValue
and t1.SomeDate =t2.SomeDate
and t1.SomeFlag = t2.SomeFlag
and t1.SomeOtherValue = t2.SomeOtherValue
where t2.SomeValue is null


set statistics time off
go
```

**Left Join**
   
SQL Server Execution Times:
     
CPU time = 7269 ms, elapsed time = 7396 ms.

**Not Exist**
   
SQL Server Execution Times:
     
CPU time = 21450 ms, elapsed time = 21574 ms.

**Left Loop Join**
   
SQL Server Execution Times:
     
CPU time = **811 ms**, elapsed time = **449 ms**.

And voilà, as you can see the _left loop join_ is much faster than either of those two other queries. Just a word of caution, do not go ahead and start changing all of your queries, the optimizer is pretty smart about what to do. But of course even the smartest ones get it wrong sometime and that is when you can use table hints and query hints to get the performance you want

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://msdn.microsoft.com/en-us/library/ms191318.aspx
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22