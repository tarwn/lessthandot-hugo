---
title: Can’t depend on sp_depends? Try using sp_refreshsqlmodule
author: SQLDenis
type: post
date: 2009-06-26T14:17:48+00:00
url: /index.php/datamgmt/datadesign/can-t-depend-on-sp_depends-try-using-sp_/
views:
  - 16218
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - howto
  - sql server 2008
  - trick

---
This will not work on SQL Server 2000 since the sp_refreshsqlmodule does not exists on that version!

A while back in the [What is deferred name resolution and why do you need to care?][1] blogpost I showed you that sp_depens is not reliable because you can create procedures that reference objects that have not been created yet.

You can use sp_refreshsqlmodule to help &#8216;fix&#8217; that
  
let&#8217;s take a look at how that works

First create this awesome stored procedure

<pre>create procedure prBla
as
select * from Blah 
go</pre>

Now execute sp_depends

<pre>exec sp_depends 'Blah'</pre>

Server: Msg 15009, Level 16, State 1, Procedure sp_depends, Line 25
  
The object &#8216;Blah&#8217; does not exist in database &#8216;tempdb&#8217; or is invalid for this operation.

So that tells us that the table Blah does not exist. Fine, what happens if we run sp_depends for the proc?

<pre>exec sp_depends 'prBla'</pre>

Object does not reference any object, and no objects reference it.

That makes sense since the table does not exist. Let&#8217;s create this table

<pre>create table Blah
(SomeCol int)</pre>

Now run sp_depends again for the table and the project

<pre>exec sp_depends 'Blah'</pre>

Object does not reference any object, and no objects reference it.

<pre>exec sp_depends 'prBla'</pre>

Object does not reference any object, and no objects reference it.

Okay so SQL server knows that the table Blah has been created but it still does not know that it is beeing used in the proc

Will executing the proc change that perhaps?

<pre>exec  prBla</pre>

<pre>exec sp_depends 'Blah'</pre>

Object does not reference any object, and no objects reference it.

<pre>exec sp_depends 'prBla'</pre>

Object does not reference any object, and no objects reference it.

Nope, no such luck, that didn&#8217;t do anything
  
Now execute the sp_refreshsqlmodule proc

<pre>exec sp_refreshsqlmodule 'prBla'</pre>

Execute sp_depends again

<pre>exec sp_depends 'Blah'</pre>

In the current database, the specified object is referenced by the following:

<pre>name		type
dbo.prBla	stored procedure</pre>

Yep, now it is showing us that table Blah is used by the stored proc prBla
  
Will it work when we run sp_depends for the stored procedure?

<pre>exec sp_depends 'prBla'</pre>

In the current database, the specified object references the following:

<pre>name		type		updated	selected	column
dbo.Blah	user table	no	yes		SomeCol</pre>

And as you can see it also shows that the table is used..like Borat would say &#8220;Very Nice&#8221;

Clean up by dropping these sample objects

<pre>drop table Blah
go
drop procedure prBla
go</pre>



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/what-is-deferred-name-resolution-and-why
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22