---
title: Dealing with The multi-part identifier “dbo.Table.Column” could not be bound. error in an update statement
author: SQLDenis
type: post
date: 2010-09-01T22:33:18+00:00
url: /index.php/datamgmt/dbprogramming/dealing-with-the-multi-part-identifier-d/
views:
  - 23050
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - error
  - gotcha
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - update

---
One of the best ways to improve your skills is by helping other people in forums and newsgroups. I was doing just that tonight and I stumbled on this piece of code here: http://stackoverflow.com/questions/3622685/transfer-column-data-from-one-database-to-another

<pre>update [DB1].[dbo].[Table1]
set [DB1].[dbo].[Table1].[Column1] = [DB2].[dbo].[Table1].[Column1]
from [DB1].[dbo].[Table1] db1Alias, [DB2].[dbo].[Table1] db2Alias
where db1Alias.TeamId = db2Alias.TeamId
and db1Alias.IndividualId = db2Alias.IndividualId</pre>

Can you tell what is wrong with the code? If you try to run that you will get the following error

<div style="border:1px solid black;background-color:#444;color:white;margin:0 20px;padding:0 5px 0 5px;">
  Msg 4104, Level 16, State 1, Line 2<br /> The multi-part identifier &#8220;DB2.dbo.Table1.Column1&#8221; could not be bound.
</div>



The problem is that aliases are defined for the tables but not used in the column part.

Let&#8217;s take a closer look with some code that you can actually run. First create these two tables

<pre>use tempdb
go

create table BlaTest(id int)
insert BlaTest values(1)
go

create table BlaTest2(id int)
insert BlaTest2 values(1)
go</pre>

Now when you try to run this piece of code, which is the same as the code at the beginning of the post except for the object names, you will get an error.

<pre>update tempdb.dbo.BlaTest
set tempdb.dbo.BlaTest.id =tempdb.dbo.BlaTest2.id
from tempdb.dbo.BlaTest b
JOIN tempdb.dbo.BlaTest2 a on b.id =a.id</pre>

Here is the error

<div style="border:1px solid black;background-color:#444;color:white;margin:0 20px;padding:0 5px 0 5px;">
  Msg 4104, Level 16, State 1, Line 2<br /> The multi-part identifier &#8220;tempdb.dbo.BlaTest2.id&#8221; could not be bound.
</div>



So what can be done?

Here is my preferred way of running this query, use the table aliases in the update and the columns

<pre>update b
set b.id =a.id
from tempdb.dbo.BlaTest b
JOIN tempdb.dbo.BlaTest2 a on b.id =a.id</pre>

But you can also write the query like this by using the alias only for the column that is not being updated.

<pre>update tempdb.dbo.BlaTest
set tempdb.dbo.BlaTest.id =a.id
from tempdb.dbo.BlaTest b
JOIN tempdb.dbo.BlaTest2 a on b.id =a.id</pre>

The important thing to remember is that you have to use the alias in the table that you are not updating, to be safe just use the alias all over, that way you don&#8217;t have to think whether to use the alias or not.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22