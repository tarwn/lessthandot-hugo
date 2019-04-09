---
title: Dealing with Cannot resolve collation conflict for equal to operation errors
author: SQLDenis
type: post
date: 2009-10-26T16:10:21+00:00
ID: 596
url: /index.php/datamgmt/datadesign/dealing-with-cannot-resolve-collation-co/
views:
  - 21605
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - collation
  - database
  - gotcha
  - programming
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tip

---
This was asked on twitter recently and I gave the answer there. I decided to write a blog post about this because I can use over 140 charaters here instead.
  
You will see the Cannot resolve collation conflict for equal to operation error when you try to join 2 tables. let's take a look at what we need to do to resolve this. First create and populate these two tables

sql
use tempdb
go



create table Test (SomeColumn varchar(100) not null)
insert Test values('bla')
insert Test values('test')


create table Test2 (SomeColumn varchar(100) collate Traditional_Spanish_CI_AI not null)
insert Test2 values('Niño')
insert Test2 values('bla')
```
Now run the following join between the Test and Test2 tables

sql
select * from Test t1
join Test2 t2 on t1.SomeColumn = t2.SomeColumn
```

Here is the error you will get

**Server: Msg 446, Level 16, State 9, Line 1
  
Cannot resolve collation conflict for equal to operation.**

To quickly fix this you can use collate in your SQL, you have to make sure that the collation is the same on both columns. So you either add the collate with the collation of the column in test2 to the column in the test table or vice-versa. Here is one example

sql
select * from Test t1
join Test2 t2 on t1.SomeColumn = t2.SomeColumn collate Traditional_Spanish_CI_AI
```

You can also apply collate on the other column…first we need to know what the default was in your database. We can use the ANSI information\_schema.columns view to get this info, we need to use the collation\_name column. Run the following query to grab it

sql
select column_name,collation_name
from information_schema.columns
where table_name = 'test'
```

In my case it is SQL\_Latin1\_General\_CP1\_CI_AS, now the query becomes the following

sql
select * from Test t1
join Test2 t2 on t1.SomeColumn collate SQL_Latin1_General_CP1_CI_AS = t2.SomeColumn
```

Just for fun let's see what the collation is for the column in the Test2 table

sql
select column_name,collation_name
from information_schema.columns
where table_name = 'Test2'
```

As expected it is Traditional\_Spanish\_CI_AI

You can use the ::fn_helpcollations()function which returns a list of all the collations supported by SQL Server to get more info about the collation

Run the following query (and yes :: is not a typo)

sql
select * from ::fn_helpcollations()
where name ='Traditional_Spanish_CI_AI'
```

The query returns the following description for Traditional\_Spanish\_CI_AI'

Traditional-Spanish, case-insensitive, accent-insensitive, kanatype-insensitive, width-insensitive

One thing to be aware of is that when using collate it needs to do a conversion so you might get performance problems…make sure to check those execution plans.

Hopefully this will help some person when dealing with this in the future.



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22