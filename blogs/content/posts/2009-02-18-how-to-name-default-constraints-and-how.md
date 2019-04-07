---
title: How To Name Default Constraints And How To Drop Default Constraint Without A Name In SQL Server
author: SQLDenis
type: post
date: 2009-02-18T15:14:30+00:00
ID: 330
excerpt: |
  Take a look at this code
  
  create table Foo2(id int,
  id2 int constraint DefaultID2 default 1)
  
  As you can see it is a simple table with 2 columns, the second column has a constraint on it named DefaultID2.
  We can verify that the table has a default&hellip;
url: /index.php/datamgmt/dbprogramming/how-to-name-default-constraints-and-how/
views:
  - 43648
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - best practice
  - pitfall
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tip
  - trick

---
Take a look at this code

sql
create table Foo2(id int,
id2 int constraint DefaultID2 default 1)
```

As you can see it is a simple table with 2 columns, the second column has a constraint on it named DefaultID2.
  
We can verify that the table has a default on that column by writing something like this

sql
select column_default 
from information_schema.columns 
where table_name = 'Foo2' 
and column_name = 'id2'
```

this is the output
  
((1))

How can you get the default name?
  
On version 7 and up run this

sql
select s.name --default name
from sysobjects s 
join syscolumns c on s.parent_obj = c.id
where s.xtype = 'd'
and c.cdefault = s.id
and parent_obj= object_id('Foo2')
and c.name ='id2'
```

On 2005 and up run this(the previous code also runs on SQL Server 2005 and SQL Server 2008)

sql
select s.name --default name
from sys.sysobjects s 
join sys.syscolumns c on s.parent_obj = c.id
where s.xtype = 'd'
and c.cdefault = s.id
and parent_obj= object_id('Foo2')
and c.name ='id2'
```

How do you drop such a constraint? Very easy you do this

sql
ALTER table foo2 drop DefaultID2
```

Now we will create a table with two default constraints and both of them will be created without a name when running the create table DDL

sql
create table Foo(id int default 0,
id2 int default 1)
```

Now let&#8217;s see if the default is created

sql
select column_default 
from information_schema.columns 
where table_name = 'Foo' 
and column_name = 'id2'
```

This still returns this output
  
((1))

All fine, now how can we drop the default for the id2 column on the Foo table?

Running this code

sql
select s.name --default name
from sys.sysobjects s 
join sys.syscolumns c on s.parent_obj = c.id
where s.xtype = 'd'
and c.cdefault = s.id
and parent_obj= object_id('Foo')
and c.name ='id2'
```

Will give use the default name, in this case it is DF\_\_Foo\_\_id2__7D439ABD
  
So now we can drop the default by doing this

sql
ALTER table foo2 drop DF__Foo__id2__7D439ABD
```

So what is the big deal you say?
  
Let&#8217;s say you do this on a staging box and want to create a script to hand over to someone else who will run it on the production box
  
If you create you script on the staging box and the person runs it on production he will see something like this

erver: Msg 3733, Level 16, State 2, Line 1
  
Constraint &#8216;DF\_\_Foo\_\_id2__7D439ABD&#8217; does not belong to table &#8216;foo2&#8217;.
  
Server: Msg 3727, Level 16, State 1, Line 1
  
Could not drop constraint. See previous errors.

Then you will get a call that your script is broken, you will tell him that it works on staging. In the end you will have to do something like this so that it can run on any server as long as the table and column name are the same

sql
declare @default sysname
declare @tableName sysname
declare @columnname sysname

select @tableName  = 'Foo' --table name
select @columnname = 'id2' --column name

--check for SQL Server Version
if coalesce(parsename(convert(varchar(50),SERVERPROPERTY ( 'ProductVersion' )),4),
	parsename(convert(varchar(50),SERVERPROPERTY ( 'ProductVersion' )),3)) > 8

	select @default= s.name --default name
	from sysobjects s 
	join syscolumns c on s.parent_obj = c.id
	where s.xtype = 'd'
	and c.cdefault = s.id
	and parent_obj= object_id(@tableName)
	and c.name =@columnname

else

	select @default= s.name --default name
	from sys.sysobjects s 
	join sys.syscolumns c on s.parent_obj = c.id
	where s.xtype = 'd'
	and c.cdefault = s.id
	and parent_obj= object_id(@tableName)
	and c.name =@columnname



--test first
print( 'alter table ' + @tableName +' drop  ' + @default )
--exec ( 'alter table ' + @tableName +' drop  ' + @default )
```

I commented out the exec and put print instead so that you can see what would get executed

A best practice is always to name your constraint because it will save you a lot of headaches down the road



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22