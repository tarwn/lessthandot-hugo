---
title: Your testbed has to have the same volume of data as on production in order to simulate normal usage
author: SQLDenis
type: post
date: 2009-06-02T13:55:54+00:00
ID: 454
url: /index.php/datamgmt/datadesign/your-testbed-has-to-have-the-same-volume/
views:
  - 10472
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - database
  - performance
  - testing

---
Your testbed has to have the same volume of data as on production otherwise you are not really testing anything.

This blogpost is kind of a rant after I noticed [this post][1] on Stackoverflow

> I do not believe there is a problem with the create trigger statement itself. The create trigger statement was successful and quick in a test environment, and the trigger works correctly when rows are inserted/updated to the table. Although when I created the trigger on the test database there was no load on the table and it had considerably less rows, which is different than on the live/production database (**100 vs. 13,000,000**+).

Now how on earth can you expect anything to behave the same when you compare 100 rows against 13 million?
  
This is one of the fundamental flaws when people design a database, move it to production and then find out that it blows up/breaks down/is unusable on production

The worst case I have seen was when someone designed a table with a CompanyName column which was CHAR(5000). Yes you read that right CHAR(5000) nor VARCHAR(5000). On 'staging' it was all fine with 50 rows or so. They moved this to production loaded it up with 100000 rows and it was slow as hell. What can you expect when you have only one row per page.....this was just terrible.

I understand that not every shop has the money to store terabytes of data but guess what? You can buy a USB TB hard drive for about $100. Plug in 5 of those and test with volume otherwise you will suffer later.

Now let's look at some code to see what the difference is

First create these two tables

```sql
create table TestSmall (id int identity not null,Somevalue char(108),SomeValue2 uniqueidentifier)
go

create table TestBig (id int identity not null,Somevalue char(108),SomeValue2 uniqueidentifier)
go
```

We will populate the small table with 256 rows and the big one with 65536 rows

```sql
--256 rows
insert TestSmall
select convert(varchar(36),newid())
 + convert(varchar(36),newid())
 + convert(varchar(36),newid()),newid() from master..spt_values t1
where t1.type = 'p'
and t1.number < 256
go

--65536 rows
insert TestBig
select convert(varchar(36),newid())
 + convert(varchar(36),newid())
 + convert(varchar(36),newid()),newid() from master..spt_values t1
outer apply master..spt_values t2
where t1.type = 'p'
and t1.number < 256
and t2.type = 'p'
and t2.number < 256
go
```

Now we will create a clustered index on each table

```sql
create clustered index ix_somevalue_small on TestSmall(Somevalue)
go
create clustered index ix_somevalue_big on TestBig(Somevalue)
go
```

Time to run some code
  
First we have to turn on statistics for time

```sql
set statistics io on
```

Now run these queries

```sql
select * from TestSmall
where Somevalue like '2%'

select * from TestBig
where Somevalue like '2%'
```

Table 'TestSmall'. Scan count 1, logical reads 2, physical reads 0
  
Table 'TestBig'. Scan count 1, logical reads 74, physical reads 0

As you can see the reads are much higher for the TestBig table, this is of course not surprising since the TestBig has a lot more rows

What will happen if we write a non sargable query by using a function in the WHERE clause?

```sql
select * from TestSmall
where left(Somevalue,1) = '2'

select * from TestBig
where left(Somevalue,1) = '2'
```

Table 'TestSmall'. Scan count 1, logical reads 7, physical reads 0
  
Table 'TestBig'. Scan count 1, logical reads 1132, physical reads 0

Okay, so the smaller table had 3.5 times more reads while the bigger table had 15 times more reads. Just imagine what would happen if the bigger table was even bigger?

Time to turn of the statistics for IO

```sql
set statistics io off
```

Now we will look at statistics for time, you can do that by running the following command

```sql
set statistics time on
```

Let's run the same queries again

```sql
select * from TestSmall
where Somevalue like '2%'

select * from TestBig
where Somevalue like '2%'
```

SQL Server Execution Times:
     
CPU time = 0 ms, elapsed time = 0 ms.

SQL Server Execution Times:
     
CPU time = 15 ms, elapsed time = 97 ms.

As you can see the numbers are much better for the smaller table
  
When we do the non sargable queries the numbers don't increase for the smaller table but they do for the bigger table

```sql
select * from TestSmall
where left(Somevalue,1) = '2'

select * from TestBig
where left(Somevalue,1) = '2'
```

SQL Server Execution Times:
     
CPU time = 0 ms, elapsed time = 0 ms.

SQL Server Execution Times:
     
CPU time = 31 ms, elapsed time = 132 ms.

Since data might be cached and you would like to start fresh every time you can execute the following command to clear the cache

```sql
dbcc freeproccache
dbcc dropcleanbuffers
```

Finally I will leave you with execution plan pics

**Sargable Query**

```sql
select * from TestSmall
where Somevalue like '2%'

select * from TestBig
where Somevalue like '2%'
```

![Sargable Query][2]

**Non Sargable Query**

```sql
select * from TestSmall
where left(Somevalue,1) = '2'

select * from TestBig
where left(Somevalue,1) = '2'
```

![Non Sargable Query][3]



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][4] forum or our [Microsoft SQL Server Admin][5] forum**<ins></ins>

 [1]: http://stackoverflow.com/questions/230642/create-trigger-is-taking-more-than-30-minutes-on-sql-server-2005/939616#939616
 [2]: http://imgur.com/9qRfI.png
 [3]: http://imgur.com/mHaI0.png
 [4]: http://forum.lessthandot.com/viewforum.php?f=17
 [5]: http://forum.lessthandot.com/viewforum.php?f=22