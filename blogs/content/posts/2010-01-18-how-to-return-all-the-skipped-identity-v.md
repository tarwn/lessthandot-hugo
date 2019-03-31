---
title: How to return all the skipped identity values from a table in SQL Server
author: SQLDenis
type: post
date: 2010-01-18T12:41:34+00:00
url: /index.php/datamgmt/dbprogramming/how-to-return-all-the-skipped-identity-v/
views:
  - 4909
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - gaps
  - how to
  - identity
  - sql server 2005
  - sql server 2008

---
Every now and then someone will ask how to return a list of all the identity values in a table that have been skipped. You will probably have a table with an identity column, the &#8216;problem&#8217; with identity columns is that if an insert is rolled back or fails in any way then the identity value is not reused&#8230;you end up with gaps. Identifying gaps is pretty easy to do if you have a table of numbers in your database.

If you don&#8217;t have a table of numbers, here is some code that will create a table with numbers between 1\` and 2048

<pre>create table Numbers (number int not null primary key )
go
insert Numbers 
select number + 1 
from master..spt_values s
where s.type='P'</pre>

Now that we have our numbers table we can proceed with creating another table which we will populate with some numbers

<pre>create table #bla(id int)

insert #bla values(1)
insert #bla values(2)
insert #bla values(4)
insert #bla values(5)
insert #bla values(9)
insert #bla values(12)</pre>

Here is the code that will return the gaps (the values 3,6,7,8,10,11) from the temp table

<pre>select number 
from Numbers n
left join #bla b on n.number = b.id
where n.number &lt; (select MAX(id) from #bla)
and  b.id is null</pre>

As you can see it is a simple left join, we also check for the max value otherwise you would get everything back that is greater than the max value in the #bla table.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22