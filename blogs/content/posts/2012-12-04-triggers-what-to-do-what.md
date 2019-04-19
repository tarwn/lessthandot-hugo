---
title: 'SQL Advent 2012 Day 4: Triggers, what to do, what not to do'
author: SQLDenis
type: post
date: 2012-12-04T10:04:00+00:00
ID: 1823
excerpt: 'This is day four of the SQL Advent 2012 series of blog posts. Today we are going to look at triggers. Triggers are a great way to keep your database in a consistent state. There are two types of triggers, DML triggers and DLL triggers. DML triggers  res&hellip;'
url: /index.php/datamgmt/dbprogramming/triggers-what-to-do-what/
views:
  - 19189
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database
  - ddl triggers
  - dml triggers
  - rdbms
  - sql
  - sql server 2008
  - sql server 2012
  - t-sql
  - triggers

---
This is day four of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at triggers. Triggers are a great way to keep your database in a consistent state. There are two types of triggers, DML triggers and DLL triggers. DML triggers respond to Data Manipulation Statements (Insert, Delete, Update) DDL triggers respond to Data Definition Language events. 

Some things that DML triggers are used for:

  * Keeps your databases from having wrong data by doing checks that can't be handled with constraints
  * Filling in values that are not supplied and can't be handled through default constraints since these don't fire on updates 
  * Calculation summary values and updates the summary table with that value
  * Used as a mechanism to maintain an audit trail for DML statements

Some things that DDL triggers are used for:

  * Automatically add columns to a table if they were not added, for example LastUpdated and InsertedBy columns
  * Notify a DBA when a database has been created, dropped or altered
  * Used as a mechanism to maintain an audit trail for DDL statements, capture every time an object has been created, dropped or altered and by who

Most common mistake people make when first starting writing triggers is that they write it in such a way that it will only work if you insert/update/delete one row at a time. A trigger fires per batch not per row, you have to take this into consideration otherwise your DML statements will blow up. How to do this is explained in this post [Best Practice: Coding SQL Server triggers for multi-row operations][2], there is no point recreating that post here.

Another problem that I see is that some people think a trigger is SQL Server's version of crontab, you will see code that sends email, kicks off jobs, runs stored procedures. This is the wrong approach, a trigger should be lean and mean, it should execute as fast as possible, if you need to do some additional things then dump some data from the trigger into a processing table and then use that table to do your additional tasks. Don't use triggers as a messaging system either, SQL Server comes with Service Broker, use that instead. Triggers might look like hammers to some people but I guarantee you not everything is a nail....

You could end up with a real difficult thing to debug, one trigger that kicks off other triggers, now have fun debugging the trigger hell you got yourself into....or worse debug this mess if you inherited this....this is like the GOTO spaghetti code of databases.

Since triggers work besides the scenes you might spend hours debugging something only to find out that a trigger modified the value

One thing I always find interesting is when someone sees two _n rows affected_ statements when they only did one insert, you know a person like that has not been exposed to triggers yet

Some people will say that you don't need triggers for anything and that they do more harm than good, I myself don't agree with that, triggers have a place but they should not be abused and overused, the same can be said of views

What is your opinion, are triggers needed or are they not needed?

That is all for day four of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][3]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/DBProgramming/MSSQLServer/best-practice-coding-sql-server-triggers
 [3]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap