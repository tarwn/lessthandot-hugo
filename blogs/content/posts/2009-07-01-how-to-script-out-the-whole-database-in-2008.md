---
title: How To Script Out The Whole Database In SQL Server 2005 and SQL Server 2008
author: SQLDenis
type: post
date: 2009-07-01T13:36:00+00:00
ID: 491
excerpt: |
  There seems to be a little bit of confusion on how to script out the database. The correct answer is of course: just run all the scripts you have in source control  :-)
  
  So for those who do not use/have source control I will show you how to do it
  
  L&hellip;
url: /index.php/datamgmt/datadesign/how-to-script-out-the-whole-database-in-2008/
views:
  - 10692
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - how to
  - scripting
  - sql server 2005
  - sql server 2008

---
There seems to be a little bit of confusion on how to script out the database. The correct answer is of course: **just run all the scripts you have in source control** 🙂

So for those who do not use/have source control I will show you how to do it

Logically you would think that this would be the way: _Right click on the database –> Script Database As –> CREATE To and then pick your choice_. See also image below



<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut1.png?mtime=1357605198"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut1.png?mtime=1357605198" width="614" height="365" /></a>
</div>

What that will do is just create the database and not much else, here is what the script might look like

```sql
USE [master]
GO

/****** Object:  Database [AdventureWorks]    Script Date: 06/30/2009 15:04:19 ******/
CREATE DATABASE [AdventureWorks] ON  PRIMARY 
( NAME = N'AdventureWorks_Data', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL10.MSSQLSERVERMSSQLDATAAdventureWorks_Data.mdf' , SIZE = 174080KB , MAXSIZE = UNLIMITED, FILEGROWTH = 16384KB )
 LOG ON 
( NAME = N'AdventureWorks_Log', FILENAME = N'C:Program FilesMicrosoft SQL ServerMSSQL10.MSSQLSERVERMSSQLDATAAdventureWorks_Log.ldf' , SIZE = 2048KB , MAXSIZE = 2048GB , FILEGROWTH = 16384KB )
GO

ALTER DATABASE [AdventureWorks] SET COMPATIBILITY_LEVEL = 100
GO

IF (1 = FULLTEXTSERVICEPROPERTY('IsFullTextInstalled'))
begin
EXEC [AdventureWorks].[dbo].[sp_fulltext_database] @action = 'enable'
end
GO
```

What you have to do is actually this: _Right click on your database–>Tasks–>Generate Scripts_

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut2.png?mtime=1357605211"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut2.png?mtime=1357605211" width="456" height="465" /></a>
</div>

Select the database you want to script and make sure you check the option at the bottom that says _Script all objects in the selected database_. See image below

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut3.png?mtime=1357605224"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut3.png?mtime=1357605224" width="493" height="442" /></a>
</div>

That is all, click next a couple of times and review your options and you are set



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22