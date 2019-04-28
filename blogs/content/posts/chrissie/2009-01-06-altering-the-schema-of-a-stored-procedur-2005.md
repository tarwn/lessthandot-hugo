---
title: Altering the Schema of a stored procedure in SQL-server 2005
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-06T06:35:29+00:00
ID: 273
url: /index.php/datamgmt/dbprogramming/mssqlserver/altering-the-schema-of-a-stored-procedur-2005/
views:
  - 9543
categories:
  - Microsoft SQL Server

---
Today I had to change the schema of a stored procedure. I couldn&#8217;t find a clicky click method to do this in SQL server management studio (which kinda worries me). So I looked on google for a method and found [this][1]. But not being an SQLguru I took a few seconds to sink in.

So the syntax is.

> <span class="MT_blue">ALTER SCHEMA</span> nameoftheschematotransferto <span class="MT_blue">TRANSFER</span> nameandschemaoftheSP

For example:

We have an SP called username.spo1 and I want to change it to dbo.spo1 so I would change it from schema username to schema dbo. This would then mean 

```tsql
ALTER SCHEMA dbo TRANSFER username.spo1```
The funny thing is that not even SQL prompt from Redgate recognizes the word TRANSFER as a keyword. So I guess transfering schemas is not a very common event.

 [1]: http://weblogs.asp.net/steveschofield/archive/2005/12/31/change-schema-name-on-tables-and-stored-procedures-in-sql-server-2005.aspx