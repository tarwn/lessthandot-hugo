---
title: Can't depend on sp_depends? Try using sp_refreshsqlmodule
author: SQLDenis
type: post
date: 2009-06-26T14:17:48+00:00
ID: 484
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

You can use sp_refreshsqlmodule to help 'fix' that
  
let's take a look at how that works

First create this awesome stored procedure

```sql
create procedure prBla
as
select * from Blah 
go
```

Now execute sp_depends

```sql
exec sp_depends 'Blah'
```

Server: Msg 15009, Level 16, State 1, Procedure sp_depends, Line 25
  
The object 'Blah' does not exist in database 'tempdb' or is invalid for this operation.

So that tells us that the table Blah does not exist. Fine, what happens if we run sp_depends for the proc?

```sql
exec sp_depends 'prBla'
```

Object does not reference any object, and no objects reference it.

That makes sense since the table does not exist. Let's create this table

```sql
create table Blah
(SomeCol int)
```

Now run sp_depends again for the table and the project

```sql
exec sp_depends 'Blah'
```

Object does not reference any object, and no objects reference it.

```sql
exec sp_depends 'prBla'
```

Object does not reference any object, and no objects reference it.

Okay so SQL server knows that the table Blah has been created but it still does not know that it is beeing used in the proc

Will executing the proc change that perhaps?

```sql
exec  prBla
```

```sql
exec sp_depends 'Blah'
```

Object does not reference any object, and no objects reference it.

```sql
exec sp_depends 'prBla'
```

Object does not reference any object, and no objects reference it.

Nope, no such luck, that didn't do anything
  
Now execute the sp_refreshsqlmodule proc

```sql
exec sp_refreshsqlmodule 'prBla'
```

Execute sp_depends again

```sql
exec sp_depends 'Blah'
```

In the current database, the specified object is referenced by the following:

<pre>name		type
dbo.prBla	stored procedure</pre>

Yep, now it is showing us that table Blah is used by the stored proc prBla
  
Will it work when we run sp_depends for the stored procedure?

```sql
exec sp_depends 'prBla'
```

In the current database, the specified object references the following:

<pre>name		type		updated	selected	column
dbo.Blah	user table	no	yes		SomeCol</pre>

And as you can see it also shows that the table is used..like Borat would say "Very Nice"

Clean up by dropping these sample objects

```sql
drop table Blah
go
drop procedure prBla
go
```



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DataDesign/what-is-deferred-name-resolution-and-why
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22