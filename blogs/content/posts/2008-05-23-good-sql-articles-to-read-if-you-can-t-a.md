---
title: Good SQL Articles To Read If You Can’t Afford Books
author: SQLDenis
type: post
date: 2008-05-23T13:11:30+00:00
ID: 34
url: /index.php/datamgmt/datadesign/good-sql-articles-to-read-if-you-can-t-a/
views:
  - 41407
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database
  - deadlocks
  - functions
  - howto
  - math
  - sql
  - sqlserver
  - tips
  - toread
  - tricks

---
You have only $50 left and you can buy two DVDs or one SQL book, what do you do? I would buy the book but not every person has the same idea of a fun time. This is the reason why I present you with a bunch of links to articles which will give you very good info. Some of this you won't be able to find in a book anyway.

[The curse and blessings of dynamic SQL][1]. How you use dynamic SQL, when you should – and when you should not.

[Arrays and Lists in SQL Server][2]. Several methods on how to pass an array of values from a client to SQL Server, and performance data about the methods. Two versions are available, one for SQL 2005 and one for SQL 2000 and earlier.

[Implementing Error Handling with Stored Procedures][3] and [Error Handling in SQL Server – a Background][4]. Two articles on error handling in SQL Server.

[The ultimate guide to the datetime datatypes][5]
  
The purpose of this article is to explain how the datetime datatypes work in SQL Server, including common pitfalls and general recommendations.

[Stored procedure recompiles and SET options][6]
  
Using stored procedures is generally considered a good thing. One advantage of stored procedures is that they are precompiled. This means that at execution time, SQL Server will fetch the precompiled procedure plan from cache memory (if exists) and execute it. This is generally faster than optimizing and compiling the code for each execution. However, under some circumstances, a procedure needs to be recompiled during execution.

[Do You Know How Between Works With Dates?][7]
  
article explaining why it can be dangerous to use between with datetime data types

[How Are Dates Stored Internally In SQL Server?][8]
  
Article explaining how datetimes are actually stored internally

Three part deadlock troubleshooting post, a must read if you want to understand how to resolve deadlocks.
  
[Deadlock Troubleshooting, Part 1][9]
  
[Deadlock Troubleshooting, Part 2][10]
  
[Deadlock Troubleshooting, Part 3][11]

[SQL Server 2005 Whitepapers List][12]
  
A list of 29 different SQL Server 2005 Whitepapers

[Keep a check on your IDENTITY columns in SQL Server][13]This article shows you how to keep an eye on your IDENTITY columns and find out before they run out of values, and fail with an arithmetic overflow error. 

[Character replacements in T-SQL][14]
  
Quite often SQL programmers are left with the dirty job of working with badly formatted strings mostly generated from external sources. Typical examples are badly structured date values, social security numbers with misplaced hyphens, badly formatted phone numbers etc. When the data set if small, in many cases, one can easily fix by a one time cleanup code snippet, but for larger sets one will need more generalized routines.

 [1]: http://www.sommarskog.se/dynamic_sql.html
 [2]: http://www.sommarskog.se/arrays-in-sql.html
 [3]: http://www.sommarskog.se/error-handling-II.html
 [4]: http://www.sommarskog.se/error-handling-I.html
 [5]: http://www.karaszi.com/SQLServer/info_datetime.asp
 [6]: http://www.karaszi.com/SQLServer/info_sp_recompile_set.asp
 [7]: http://dotnetsamplechapters.blogspot.com/2007/09/do-you-know-how-between-works-with.html
 [8]: http://dotnetsamplechapters.blogspot.com/2007/09/how-are-dates-stored-internally-in-sql.html
 [9]: http://blogs.msdn.com/bartd/archive/2006/09/09/Deadlock-Troubleshooting_2C00_-Part-1.aspx
 [10]: http://blogs.msdn.com/bartd/archive/2006/09/13/Deadlock-Troubleshooting_2C00_-Part-2.aspx
 [11]: http://blogs.msdn.com/bartd/archive/2006/09/25/deadlock-troubleshooting-part-3.aspx
 [12]: http://dotnetsamplechapters.blogspot.com/2007/03/sql-server-2005-whitepapers-list.html
 [13]: http://vyaskn.tripod.com/sql_server_check_identity_columns.htm
 [14]: http://www.projectdmx.com/tsql/strcleanup.aspx