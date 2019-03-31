---
title: SQL Server does support regular expressions in check constraints, triggers are not always needed
author: SQLDenis
type: post
date: 2009-05-26T13:00:38+00:00
url: /index.php/datamgmt/dbprogramming/sql-server-does-support-regular-expressi/
views:
  - 18105
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - check constraints
  - how to
  - pitfall
  - sql server
  - tip

---
Someone posted the following question

> I need to add table called group with a column called code
> 
> How do I add a check constraint to the column so it will only allow the following alphabetic characters (D, M, O, P or T) followed by 2 numeric characters.

Someone posted the following answer

> You cannot do this out of the box &#8211; MS SQL Server does support CHECK CONSTRAINTS &#8211; but for things like a maximum or minimum INT value, or a string length or such.
> 
> What you&#8217;re looking for would be a CHECK based on a regular expression &#8211; and out of the box, SQL Server does not offer that capability.
> 
> You could theoretically write a .NET assembly, deploy it inside SQL Server, and then use it to enforce the CHECK &#8211; not a trivial undertaking.

While SQL server does not support a full implementation of regular expression, you can do what the person asked for without a problem in T-SQL.

Here is what the regular expression looks like \[DMOPT\]\[0-9\][0-9], that will alllow allow the following alphabetic characters (D, M, O, P or T) followed by 2 numeric characters.

Enough talking let&#8217;s look at some code, first create this table

<pre>create table blatest(code char(3))</pre>

now add the check constraint

<pre>alter table blatest add  constraint ck_bla 
check (code like '[DMOPT][0-9][0-9]' )
GO</pre>

Now we can run some tests

<pre>insert blatest values('a12') --fails
insert blatest values('M12')  --good
insert blatest values('D12') --good
insert blatest values('DA1') --fails</pre>

As you can see we got the following message twice
  
_Server: Msg 547, Level 16, State 1, Line 1
  
The INSERT statement conflicted with the CHECK constraint &#8220;ck_bla&#8221;. The conflict occurred in database &#8220;Test&#8221;, table &#8220;dbo.blatest&#8221;, column &#8216;code&#8217;.
  
The statement has been terminated.
  
_ 

If you want to insert D12 but not d12, in other words you need the constraint to be case sensitive then you have to create the constraint like this
  
check (code like &#8216;\[DMOPT\]\[0-9\][0-9]&#8217; COLLATE SQL\_Latin1\_General\_CP1\_CS_AS )

What we did is used the SQL\_Latin1\_General\_CP1\_CS_AS collation, to find out what this collation does, run the following

<pre>select * from ::fn_helpcollations()
where name = 'SQL_Latin1_General_CP1_CS_AS'</pre>

Here is what is returned as the description

Latin1-General, case-sensitive, accent-sensitive,
  
kanatype-insensitive, width-insensitive for Unicode Data,
  
SQL Server Sort Order 51 on Code Page 1252 for non-Unicode Data

Let&#8217;s create the constraint, first we need to drop the old constraint

<pre>alter table blatest drop  constraint ck_bla
go</pre>

Now we will create the new case sensitive constraint

<pre>alter table blatest add  constraint ck_bla 
check (code like '[DMOPT][0-9][0-9]' COLLATE SQL_Latin1_General_CP1_CS_AS )
GO</pre>

<pre>insert blatest values('D12') --good
insert blatest values('d12') --fails</pre>

The insert with D12 will succeed but the one with d12 will not.

As you can see you can use regular expressions in check constraints, there is no need to use a trigger in a case like this.



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22