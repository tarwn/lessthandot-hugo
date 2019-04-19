---
title: Displaying missing dates by utilizing a calendar table
author: SQLDenis
type: post
date: 2013-01-17T23:59:00+00:00
ID: 1922
excerpt: |
  We got a question today from a user who wanted to display counts of zero where dates were missing. For example if you had the following data
  
  OrderDate	SomeCount
  2013-01-01	2
  2013-01-02	2
  2013-01-03	1
  2013-01-05	2
  2013-01-07	1
  2013-01-08	1
  2013&hellip;
url: /index.php/datamgmt/datadesign/displaying-missing-dates-by-utilizing/
views:
  - 23302
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - calendar
  - dates
  - left join

---
We got a question today from a user who [wanted to display counts of zero where dates were missing][1]. For example if you had the following data

<pre>OrderDate	SomeCount
2013-01-01	2
2013-01-02	2
2013-01-03	1
2013-01-05	2
2013-01-07	1
2013-01-08	1
2013-01-09	2
</pre>

What you really wanted is the following

<pre>OrderDate	SomeCount
2013-01-01	2
2013-01-02	2
2013-01-03	1
<strong>2013-01-04	0</strong>
2013-01-05	2
<strong>2013-01-06	0</strong>
2013-01-07	1
2013-01-08	1
2013-01-09	2</pre>

As you can see we added January 4th and January 6th to the results with a count of zero.

Let's get started, first we are going to created a calendar table

Here is a simple way to return a bunch of dates

sql
;with cte as (select row_number() over(order by s1.name) as Row
from sysobjects s1
cross join sysobjects s2)


select  dateadd(day,Row,'20091231') from cte
where Row < 5114
```

That will return dates between 2010-01-01 and 2023-12-31

Now let's create the calendar table and insert the rows from the query above

sql
CREATE TABLE Calendar (SomeDate date not null primary key)
GO

;with cte as (select row_number() over(order by s1.name) as Row
from sysobjects s1
cross join sysobjects s2)

INSERT Calendar
select  dateadd(day,Row,'20091231') from cte
where Row < 5114
```

You might want to adjust the range to start earlier or end later for your purpose

Now that the calendar table is ready, let's create a fake order table with some dates

sql
create table SomeData (OrderDate date, SomeCol int)
insert SomeData values('2013-01-01',1)
insert SomeData values('2013-01-01',1)
insert SomeData values('2013-01-02',1)
insert SomeData values('2013-01-02',1)
insert SomeData values('2013-01-03',1)
insert SomeData values('2013-01-05',1)
insert SomeData values('2013-01-05',1)
insert SomeData values('2013-01-07',1)
insert SomeData values('2013-01-08',1)
insert SomeData values('2013-01-09',1)
insert SomeData values('2013-01-09',1)
```

Querying from this table.....

sql
select OrderDate, count(somecol) as SomeCount
from SomeData
group by OrderDate
```

Here are the results

<pre>OrderDate	SomeCount
2013-01-01	2
2013-01-02	2
2013-01-03	1
2013-01-05	2
2013-01-07	1
2013-01-08	1
2013-01-09	2</pre>

As you can see January 4th and January 6th are missing. Let's do a left join with the calendar table to display the results

Here is the code, I will go over it a little later

sql
-- grab min and max dates or supply range yourself
declare @mindate date,@maxdate date
select @mindate =min(OrderDate),@maxdate = max(OrderDate)
from SomeData
 
--here is the query
select c.SomeDate,coalesce(x.SOmeCount,0) as TheCount
from Calendar c
left join (
select OrderDate, count(somecol) as SOmeCount
from SomeData
group by OrderDate) x
on c.SomeDate = x.OrderDate
where c.SomeDate between @mindate  and @maxdate
order by c.SomeDate
```

Running that will give you this

<pre>SomeDate	TheCount
2013-01-01	2
2013-01-02	2
2013-01-03	1
2013-01-04	0
2013-01-05	2
2013-01-06	0
2013-01-07	1
2013-01-08	1
2013-01-09	2</pre>

So what are we doing in the code? Here it is again

sql
-- grab min and max dates or supply range yourself
declare @mindate date,@maxdate date
select @mindate =min(OrderDate),@maxdate = max(OrderDate)
from SomeData
 
--here is the query
select c.SomeDate,coalesce(x.SOmeCount,0) as TheCount
from Calendar c
left join (
select OrderDate, count(somecol) as SOmeCount
from SomeData
group by OrderDate) x
on c.SomeDate = x.OrderDate
where c.SomeDate between @mindate  and @maxdate
order by c.SomeDate
```

On line 2,3 and 4 we are grabbing the min and max dates and storing those in variables which we will use in the WHERE clause on line 14
  
On line 7 we grab the date from the calendar table and also use the coalesce function to display 0 for the non existing rows which will come back as NULLS
  
Line 9 is the LEFT JOIN
  
Line 10 until 12 is the subquery where we are grouping the rows based on date, you also see x on line 12, that is the alias for the sub query
  
Line 13 has the join condition between the calendar table aliased c and the subquery aliased x
  
Line 14 is where we use the min and max date from lines 2,3 and 4
  
Line 15 does the sorting

Pretty simple right?

Your calendar table does not have to be that simple, you can add IsWeekday, IsWeekend, IsHoliday and other flags, you can also add the names of the dates

You can also have these be computed columns, for example here we are adding a computed column that will have the week number

sql
ALTER TABLE calendar ADD WeekNum AS DATEPART(wk,SomeDate)
```

Now you can see that the column is showing up

```
SELECT * FROM Calendar
```

<pre>SomeDate	WeekNum
2010-01-01	1
2010-01-02	1
2010-01-03	2
2010-01-04	2
2010-01-05	2
.....           ...</pre>

And if you add rows, you don't have to update the column

sql
INSERT Calendar values('20240101')
```

Select what we just inserted

```text
SELECT * FROM Calendar
where SomeDate = '20240101'
```

And here is the result

<pre>SomeDate	WeekNum
2024-01-01	1
</pre>

That is it for this post, hopefully it will help out somebody and hopefully someone will be adding a calendar table today

 [1]: http://forum.ltd.local/viewtopic.php?f=17&t=18042