---
title: Dammit people, get to know check constraints and use them!
author: SQLDenis
type: post
date: 2012-04-03T23:05:00+00:00
excerpt: "A couple of months back we were interviewing people for 2 positions, one of the questions I like to ask is the following: If you have a column in a table that's an integer data type how can you restrict the values to be between 1 and 10? Most of the peo&hellip;"
url: /index.php/datamgmt/datadesign/dammit-people-get-to-know/
views:
  - 13332
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - check constraints
  - ow to
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - tip
  - trick

---
A couple of months back we were interviewing people for 2 positions, one of the questions I like to ask is the following: If you have a column in a table that&#8217;s an integer data type how can you restrict the values to be between 1 and 10? Most of the people start by saying that they restrict it in the application, when I ask how they would prevent from someone who has write access to do it they usually say that they would add a trigger. Only about 20% of the people know that there is something in the table designer where they can enter a range. Between 5 and 10% of the people know that this is called a **check constraint**. If you know what a check constraint is&#8230;bravo, you my friend are an elitist!

I forgot about these interviews but this question [Overriding the maximum value of a bigint datatype in MSSQL][1] made it reappear like a phoenix that rises from the ashes.

Let&#8217;s look at some examples

First create this table

<pre>create table SomeTable(code char(3) not null)
GO</pre>

Now let&#8217;s say we want to restrict the values that you can insert to only accept characters from a through z, here is what the constraint looks like

<pre>alter table SomeTable add  constraint ck_bla
check (code like '[a-Z][a-Z][a-Z]' )
GO</pre>

If you now run the following insert statement&#8230;.

<pre>insert SomeTable values ('123')</pre>

You get this error message back

_Msg 547, Level 16, State 0, Line 1
  
The INSERT statement conflicted with the CHECK constraint &#8220;ck_bla&#8221;. The conflict occurred in database &#8220;tempdb&#8221;, table &#8220;dbo.SomeTable&#8221;, column &#8216;code&#8217;.
  
The statement has been terminated._

What if you have a tinyint column but you want to make sure that values are less then 100? Easy as well, first create this table

<pre>create table SomeTable2(SomeCol tinyint not null)
GO</pre>

Now add this constraint

<pre>alter table SomeTable2 add  constraint ck_SomeTable2
check (SomeCol &lt; 100 )
GO</pre>

Try to insert the value 100

<pre>insert SomeTable2 values ('100')</pre>

_Msg 547, Level 16, State 0, Line 2
  
The INSERT statement conflicted with the CHECK constraint &#8220;ck_SomeTable2&#8221;. The conflict occurred in database &#8220;tempdb&#8221;, table &#8220;dbo.SomeTable2&#8221;, column &#8216;SomeCol&#8217;.
  
The statement has been terminated._

Okay, what happens if you try to insert -1?

<pre>insert SomeTable2 values ('-1')</pre>

_Msg 244, Level 16, State 1, Line 1
  
The conversion of the varchar value &#8216;-1&#8217; overflowed an INT1 column. Use a larger integer column.
  
The statement has been terminated._

As you can see you also get an error, however this is not from the constraint but the error is raised because the tinyint datatype can&#8217;t be less than 0

Check constraint can also be tied to a user defined function and you can also use regular expressions. Ranges can also be used, for example _salary >= 15000 AND salary <= 100000_

Check (no pun intended) out this post also [SQL Server does support regular expressions in check constraints, triggers are not always needed][2], it has examples about creating a case sensitive check constraint

 [1]: http://stackoverflow.com/questions/10002798/overriding-the-maximum-value-of-a-bigint-datatype-in-mssql
 [2]: /index.php/DataMgmt/DBProgramming/sql-server-does-support-regular-expressi