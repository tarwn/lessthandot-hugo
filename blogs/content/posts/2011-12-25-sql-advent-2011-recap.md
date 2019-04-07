---
title: SQL Advent 2011 Recap
author: SQLDenis
type: post
date: 2011-12-25T10:22:00+00:00
ID: 1464
excerpt: |
  Here is a recap of all the 24 SQL Advent 2011 posts
  Day 1: Date and time
  In this post I covered the new date, datetime2 and time datatypes
    
  Day 2: System tables and catalog views
  In this post we took a look what the replacements are for the all s&hellip;
url: /index.php/datamgmt/datadesign/sql-advent-2011-recap/
views:
  - 5475
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - database
  - design
  - how to
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tables

---
SQL Advent 2011 has come to an end. I had lots of fun writing these but I wished I started earlier instead of 2 days before December 1, on some days I was really racing against the clock and I feel like I made some posts much shorter than I had in mind initially. But it is what it is, I hope you enjoyed them, maybe I will do this again next year.

Here is a recap of all the 24 SQL Advent 2011 posts.

Day 1: [Date and time][1]
  
In this post I covered the new date, datetime2 and time datatypes

Day 2: [System tables and catalog views][2]
  
In this post we took a look what the replacements are for the all system tables and also gave you a table with the new catalog view/compatibility view equivalent of the old system table

Day 3: [Partitioning][3]
  
In this post I looked at partitioning in pre sql 2005 days by showing you how to create partitioned views. I also showed you how to user partitioned function in sql 2005 and up

Day 4: [Schemas][4]
  
In this post I show you what schemas are and how they can help with security and logical grouping of objects

Day 5: [Common Table Expressions][5]
  
The Common Table Expressions post showed you what Common Table Expressions are and how they can be used to simplify your code

Day 6: [Windowing functions][6]
  
The Windowing functions post showed you how to do different kinds of rankings

Day 7: [Crosstab with PIVOT][7]
  
This post was all about pivoting/transposing/crosstabbing data with the PIVOT operator, also was shown how to do it dynamically

Day 8: [UNPIVOT][8]
  
This post showed you how to use UNPIVOT to get the reversed effect of PIVOT

Day 9: [Dynamic TOP][9]
  
The dynamic TOP post showed you how to do dynamic TOP without dynamic SQL or SET ROWCOUNT

Day 10: [Upsert by using the Merge statement][10]
  
This post was all about how to use MERGE to do an UPSERT (Update if it exists otherwise insert)

Day 11: [DML statements with the OUTPUT clause][11]
  
This post showed the usefulness of the OUTPUT clause

Day 12: [Table Value Constructor][12]
  
This post showed you how to use Table Value Constructor

Day 13: [DDL Triggers][13]
  
The DDL trigger post showed you how to use DDL triggers and also explained why you might want to use them

Day 14: [EXCEPT and INTERSECT SET Operations][14]
  
This post was all about the two new SET Operations EXCEPT and INTERSECT 

Day 15: [Joins][15]
  
This post showed you how to use the newer ANSI SQL JOIN syntax and also showed you what was deprecated

Day 16: [CROSS APPLY and OUTER APPLY][16]
  
Shown was how to use APPLY with derived tables as well as functions

Day 17: [varchar(max)][17]
  
In this post I showed you why varchar(max) is much better than the text data type

Day 18: [Table-valued Parameters][18]
  
I showed you how to use Table-valued Parameters to pass around tables

Day 19: [Filtered Indexes][19]
  
In this post I showed you how to create a filtered index and why it can be beneficial in your database

Day 20: [Indexes with Included Columns][20]
  
On this day I showed you how to cover you query by using Indexes with Included Columns

Day 21: [TRY CATCH][21]
  
Error handling go better in SQL Server 2005 and I show you how to use TRY CATCH

Day 22: [Dynamic Management Views][22]
  
In this post I show how you can use Dynamic Management Views to get all kinds of information about your server and databases

Day 23: [OBJECT_DEFINITION][23]
  
The OBJECT\_DEFINITION covers ways to get the text of an object and also show you why it is better than sp\_helptext or syscomments

Day 24: [Index REBUILD and REORGANIZE][24]
  
This post is all about rebuilding and reorganizing(defragmenting) indexes

There are tons of other things that I did not cover, here is just a small list of them

SQLCLR
  
ServiceBroker
  
FILESTREAM
  
Geometry, Geography and HierarchyID data types
  
Transparent Data Encryption
  
DB Mirroring
  
Policy Management
  
Resource Governor

Tomorrow we will finally look at some new and shiny T-SQL enhancements in SQL Server 2012, I will provide a list of all the new SQL Server 2012 post I have written in the last 6 months or so

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-1
 [2]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-2
 [3]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-3
 [4]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-4
 [5]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-5
 [6]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-6
 [7]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-7
 [8]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-8
 [9]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-9
 [10]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-10
 [11]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-11
 [12]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-12
 [13]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-13
 [14]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-14
 [15]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-15
 [16]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-16
 [17]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-17
 [18]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-18
 [19]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-19
 [20]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-20
 [21]: /index.php/DataMgmt/DBProgramming/MSSQLServer/try-catch-sql-advent-2011
 [22]: /index.php/DataMgmt/DataDesign/dynamic-management-views
 [23]: /index.php/DataMgmt/DBProgramming/MSSQLServer/object_definition-sql-advent-2011-day
 [24]: /index.php/DataMgmt/DataDesign/index-rebuild-and-reorganize-sql