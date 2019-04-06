---
title: A couple of ways of getting the top 2 distinct values from a set in SQL Server
author: SQLDenis
type: post
date: 2009-07-08T17:14:10+00:00
url: /index.php/datamgmt/datadesign/a-couple-of-ways-of-getting-the-top-2-di/
views:
  - 16302
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - queries
  - sql
  - transact-sql

---
In this blog post I will show you a couple of ways of getting the top 2 values from a set in SQL Server. The dense_rank queries will not work if you have SQL server 2000 but the other ones should.

Let&#8217;s say you have a table that has this data
  
100
  
100
  
100
  
99
  
99
  
95
  
95
  
90

You want to get the 2 highest amounts in that table the values 100 and 99, how would you do this?
  
Let&#8217;s take a look at some posibillities, first create this table and populate it with data

<pre>create table TestTies (id int identity,SomeValue tinyint)

insert TestTies values(100)
insert TestTies values(100)
insert TestTies values(100)
insert TestTies values(99)
insert TestTies values(99)

insert TestTies values(95)
insert TestTies values(95)
insert TestTies values(90)</pre>

Top 2 obviously will not work

<pre>select top 2 id, SomeValue
from TestTies
order by SomeValue desc</pre>

<pre>output
-----------------
id	SomeValue
1	100
2	100</pre>

You also cannot use WITH TIES because that just brings the value 100

<pre>select top 2  WITH TIES id, SomeValue
from TestTies
order by SomeValue desc</pre>

<pre>Output
-----------------
id	SomeValue
1	100
2	100
3	100</pre>

Here are a couple of ways that will work

For all the queries below the ouput will be this

<pre>Output
------------------
id	SomeValue
1	100
2	100
3	100
4	99
5	99</pre>

The first one is by using the DENSE_RANK() function. The queries below are functionally identical, one is using a Common Table Expression while the other one is using a subquery

<pre>--query 1
with rankings as (
select *,DENSE_RANK() OVER ( ORDER BY SomeValue desc)  as Rank 
from TestTies)

select id, SomeValue from rankings
where Rank &lt;=2</pre>

<pre>--query 2
select id, SomeValue from   (
select *,DENSE_RANK() OVER ( ORDER BY SomeValue desc)  as Rank 
from TestTies) x
where Rank &lt;=2</pre>

We can also use the MAX function twice like in the query below

<pre>--query 3
select *
from TestTies
where SomeValue &gt;= (select max(SomeValue) 
			from TestTies
			where SomeValue &lt; (select max(SomeValue) 
			from TestTies))</pre>

Another option is to use distinct top 2 in a sub query

<pre>--query 4
select *
from TestTies
where SomeValue in(
select distinct top 2  SomeValue
from TestTies
order by SomeValue desc)</pre>

Finally in query 5 we do a running count, as you can see that looks complicated

<pre>--query 5
select l.id, l.SomeValue
from(select v.SomeValue, v.id,
	Ranking =       (select count(distinct SomeValue) 
		   	from TestTies a
			where v.SomeValue &lt;= a.SomeValue)
	from TestTies v) l
where l.Ranking &lt;=2
order by l.Ranking </pre>

So how do these queries perform in regards to each other?

Hit CTRL + K, select all the code in the code block below and hit F5/execute

<pre>--query 1
with rankings as (
select *,DENSE_RANK() OVER ( ORDER BY SomeValue desc)  as Rank 
from TestTies)

select id, SomeValue from rankings
where Rank &lt;=2

--query 2
select id, SomeValue from   (
select *,DENSE_RANK() OVER ( ORDER BY SomeValue desc)  as Rank 
from TestTies) x
where Rank &lt;=2



--query 3
select *
from TestTies
where SomeValue &gt;= (select max(SomeValue) 
			from TestTies
			where SomeValue &lt; (select max(SomeValue) 
			from TestTies))


--query 4
select *
from TestTies
where SomeValue in(
select distinct top 2  SomeValue
from TestTies
order by SomeValue desc)



--query 5
select l.id, l.SomeValue
from(select v.SomeValue, v.id,
	Ranking =       (select count(distinct SomeValue) 
		   	from TestTies a
			where v.SomeValue &lt;= a.SomeValue)
	from TestTies v) l
where l.Ranking &lt;=2
order by l.Ranking </pre>

Here is the result

query 1 9.89% (dense_rank CTE)
  
query 2 9.89% (dense_rank sub query)
  
query 3 6.70% (max twice)
  
query 4 19.41% (distinct sub query)
  
query 5 54.11% (running count)

Wow, query 5 running count is slower than the other 4 combined, this was expected of course. It is also interesting that dense_rank is not as efficient as using max twice
  
Let&#8217;s do some more testing, we will create a non clustered index on the SomeValue column

<pre>create index ix_SomeValue on TestTies(SomeValue desc)</pre>

Run the 5 queries again

query 1 13.58% (dense_rank CTE)
  
query 2 13.58% dense_rank sub query)
  
query 3 24.03% (max twice)
  
query 4 13.95% (distinct sub query)
  
query 5 34.32% (running count)

As you can see now dense_rank is fastest. Let&#8217;s make that non clustered index a clustered index and look at the plans again.

<pre>drop index  TestTies.ix_SomeValue

create clustered index ix_SomeValue on TestTies(SomeValue desc)</pre>

Below are the results of running those queries again

query 1 7.78% (dense_rank CTE)
  
query 2 7.78% dense_rank sub query)
  
query 3 23.32% (max twice)
  
query 4 15.95% (distinct sub query)
  
query 5 45.16% (running count)

As you can see when we have an index on the column then dense_rank is the fastest out of all. Feel free to load up some more data into the table and experiment with these queries. If you know of another way to accomplish this feel free to post a comment with your query.



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22