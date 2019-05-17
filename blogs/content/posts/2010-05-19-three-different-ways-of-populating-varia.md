---
title: Three different ways of populating variables with configuration values in SQL Server
author: SQLDenis
type: post
date: 2010-05-19T22:58:00+00:00
ID: 795
excerpt: |
  I have a bunch of processes that run at then end of the day. Some of these processes are configured dynamic since table names, server names, database names and a whole bunch of other stuff might change.
  SO you migh have a (over simplified here) table l&hellip;
url: /index.php/datamgmt/dbprogramming/three-different-ways-of-populating-varia/
views:
  - 7674
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - howto
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tip

---
I have a bunch of processes that run at then end of the day. Some of these processes are configured dynamic since table names, server names, database names and a whole bunch of other stuff might change.
  
So you might have a (over simplified here) table like this

<pre>Typeid	        TypeName		TypeValue
1		ActiveServerName	SQLDenisDB1
1		DatabaseName		MyDB
1		LogTableName		LogFileTable</pre>

And there might be a dozen more configurations for a process
  
In general people will do 3 selects if there are 3 values, let's take a look at what I mean. First create this table

```sql
CREATE TABLE SomeConfigurations(Typeid INT NOT NULL, 
			TypeName VARCHAR(100), 
			TypeValue VARCHAR(100))

INSERT SomeConfigurations VALUES (1,'ActiveServerName','SQLDenisDB1')
INSERT SomeConfigurations VALUES (1,'DatabaseName','MyDB')
INSERT SomeConfigurations VALUES (1,'LogTableName','LogFileTable')
```

## One select per value

This is what I usually see, one query for each value.

```sql
DECLARE @ActiveServerName VARCHAR(100),
		@DatabaseName VARCHAR(100),
		@LogTableName VARCHAR(100)
		

SELECT @ActiveServerName = TypeValue
FROM SomeConfigurations
WHERE Typeid =1
AND TypeName = 'ActiveServerName'

SELECT @DatabaseName = TypeValue
FROM SomeConfigurations
WHERE Typeid =1
AND TypeName = 'DatabaseName'

SELECT @LogTableName = TypeValue
FROM SomeConfigurations
WHERE Typeid =1
AND TypeName = 'LogTableName'

SELECT @ActiveServerName,@DatabaseName,@LogTableName
```

Okay so that is not really something I want to maintain. I guess if you get paid by lines of code written it makes you look good 🙂
  
Next up.. a different approach...

## Enter the pivot.

Pivot was introduced in SQL Server 2005 and here is how you can change those three selects into one select

```sql
DECLARE @ActiveServerName VARCHAR(100),
		@DatabaseName VARCHAR(100),
		@LogTableName VARCHAR(100)

SELECT @ActiveServerName = ActiveServerName,
@DatabaseName = DatabaseName,
@LogTableName = LogTableName
FROM
(SELECT TypeName,TypeValue
FROM SomeConfigurations
WHERE Typeid =1) AS pivTemp
PIVOT
(   MAX(TypeValue)
    FOR TypeName IN (ActiveServerName,DatabaseName,LogTableName)
) AS pivTable

SELECT @ActiveServerName,@DatabaseName,@LogTableName
```

I prefer that over those three selects any time. Not only is it less code but it is also faster because it touches the tables once. If you want to learn more about PIVOT and UNPIVOT, then visit the following books on line link: http://msdn.microsoft.com/en-us/library/ms177410.aspx

## If you are running software from the last century: case to the rescue

Even for you people who are still on SQL Server 2000 there is a way to do this in one select, take a look at the query below

```sql
DECLARE @ActiveServerName VARCHAR(100),
		@DatabaseName VARCHAR(100),
		@LogTableName VARCHAR(100)

SELECT 
@ActiveServerName	= MAX(CASE TypeName WHEN 'ActiveServerName' THEN TypeValue ELSE '' END),
@DatabaseName		= MAX(CASE TypeName WHEN 'DatabaseName'		THEN TypeValue ELSE '' END),
@LogTableName		= MAX(CASE TypeName WHEN 'LogTableName'		THEN TypeValue ELSE '' END) 
FROM SomeConfigurations
WHERE Typeid =1

SELECT @ActiveServerName,@DatabaseName,@LogTableName
```

And the same applies here as to pivot, this query will hit the table only one time.

So in the end, which do you prefer?

A) 3 selects (because you get paid by lines of code)
  
B) Pivot (because nobody else understand pivot syntax, so this is job security)
  
C) Case statement (because you are still running NT 4.0 with SQL Server 2000 SP4)

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22