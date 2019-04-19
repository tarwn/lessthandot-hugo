---
title: Create a sorted view in SQL Server 2005 and SQL Server 2008
author: SQLDenis
type: post
date: 2008-11-05T17:20:08+00:00
ID: 195
excerpt: |
  I saw that some people are hitting our site with a search for how to create a sorted view in SQL Server 2008.
  
  
  You all know that in SQL Server 2000 you can create a view and use TOP 100 PERCENT with ORDER By and it will be sorted. Since SQL server 2&hellip;
url: /index.php/datamgmt/datadesign/create-a-sorted-view-in-sql-server-2005-2008/
views:
  - 47937
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - pitfalls
  - sql server 2005
  - sql server 2008
  - views

---
I saw that some people are hitting our site with a search for how to create a sorted view in SQL Server 2008.

You all know that in SQL Server 2000 you can create a view and use TOP 100 PERCENT with ORDER By and it will be sorted. Since SQL server 2005 that doesn't work anymore. I actually never understood the need for sorted views to begin with, how hard is it to do something like this

```sql
SELECT * 
FROM View
ORDER By Column
```

Not hard, I guess pople want the convenience of opening the view in SSMS and it is sorted 'correctly'
  
There is a way to get this to work in SQL server 2005, there is a hotfix that will 'fix' this but you have to run in 2000 compatability mode.
  
The link to the fix is here: [FIX: When you query through a view that uses the ORDER BY clause in SQL Server 2008, the result is still returned in random order][1]

Now let's get started with the code
  
Create this table

```sql
create table TestSort (id int not null)
insert TestSort values(1)
insert TestSort values(3)
insert TestSort values(4)
insert TestSort values(5)
insert TestSort values(2)
insert TestSort values(7)
insert TestSort values(9)
insert TestSort values(6)
```

And create the view

```sql
create view vTestSort
as
select top 100 percent id from TestSort
order by id
```

Now do a select from the view

```sql
select * from vTestSort
```

(result set)
  
1
  
3
  
4
  
5
  
2
  
7
  
9
  
6

Oops it is not sorted
  
Let's try something else, we will use 99.99 percent

```sql
create view vTestSort2
as
select top 99.99 percent  id from TestSort
order by id
```

Run the select against the view

```sql
select * from vTestSort2
```

(result set)
  
1
  
2
  
3
  
4
  
5
  
6
  
7
  
9

look at that, magic! It works

Let's try another way by using the max value of an integer

```sql
create view vTestSort3
as
select top 2147483648 id from TestSort
order by id
```

Run the select against the view

```sql
select * from vTestSort3
```

(result set)
  
1
  
2
  
3
  
4
  
5
  
6
  
7
  
9

And bingo, it also works.

Now, just because this works right now it doesn't mean that it will work after you apply the next hotfix or service pack. Why not doing this instead

```sql
select * from vTestSort3
order by id
```

That will always work and you don't have to deal with unexpected results down the road

 [1]: http://support.microsoft.com/default.aspx?scid=kb;en-us;926292&sd=rss&spid=2855