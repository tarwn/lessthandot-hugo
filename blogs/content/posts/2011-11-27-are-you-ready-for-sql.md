---
title: Are you ready for SQL Server 2012 or are you still partying like it is 1999?
author: SQLDenis
type: post
date: 2011-11-27T12:42:00+00:00
ID: 1397
excerpt: 'SQL Server 2012 is around the corner, perhaps you are ready to upgrade and perhaps you are not. Maybe you just have upgraded to SQL Server 2008 R2 without software assurance and thus you don’t qualify for SQL Server 2012 upgrades..  Even though most peo&hellip;'
url: /index.php/datamgmt/datadesign/are-you-ready-for-sql/
views:
  - 12015
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - skills. skillset
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
SQL Server 2012 is around the corner, perhaps you are ready to upgrade and perhaps you are not. Maybe you just have upgraded to SQL Server 2008 R2 without software assurance and thus you don’t qualify for SQL Server 2012 upgrades.. Even though most people are on SQL Server 2005 or higher these days, I see plenty of database code that is being written today in SQL Server 2000 syntax; I guess old habits are hard to break indeed! In the next 25 days I want to take a stab to identify 25 of these habits and see if we can bring them to this century. Think of it as the “SQL Server upgrade your code” advent calendar.

Another reason I want to do is because some of the 2000 syntax or pre 2000 syntax won’t work anymore in SQL Server 2012 Are you still using old style left joins with \*= and =\*? If so then you will be greeted with the following message: Incorrect syntax near &#8216;*&#8217;.

Most people who upgrade do something like this: backup the database, restore on the new server, sometimes set the compatibility level to whatever is the current one and they are done. This will work of course but you can do much better, why not changing some of the code to take advantage of new features, maybe all you want is the date instead of datetime, just making this change will save 5 bytes per row.

Another advantage of using new functionality is that it is better for your career. I interview plenty of people who have SQL Server 2005 and 2008 on their resume, I have yet to find one person who can write the ROW_NUMBER() syntax on the whiteboard for me. The snapshot isolation level is another mystery to the people I have interviewed. The blessing of an IT worker is that you will never be bored, there is always something to learn. The curse is the same as the blessing, if you don’t keep up then you become obsolete. Your job really doesn’t end when you get home, you have to spend an additional 10 -20 hours a week upgrading your skills, this can be done by listening to podcasts, messing around with the latest CTPs, answering question in forums , reading blogs and maintaining your own technical blog

Hopefully I didn’t ramble on too much about keeping your skills up to date but it is very important!!

December 1st I will have part 1 out of 25 posted, keep coming back every day and we will do a recap after day 25.

Day 1: [Date and time][1]
  
Day 2: [System tables and catalog views][2]
  
Day 3: [Partitioning][3]
  
Day 4: [Schemas][4]
  
Day 5: [Common Table Expressions][5]
  
Day 6: [Windowing functions][6]
  
Day 7: [Crosstab with PIVOT][7]
  
Day 8: [UNPIVOT][8]
  
Day 9: [Dynamic TOP][9]
  
Day 10: [Upsert by using the Merge statement][10]
  
Day 11: [DML statements with the OUTPUT clause][11]
  
Day 12: [Table Value Constructor][12]
  
Day 13: [DDL Triggers][13]
  
Day 14: [EXCEPT and INTERSECT SET Operations][14]
  
Day 15: [Joins][15]
  
Day 16: [CROSS APPLY and OUTER APPLY][16]
  
Day 17: [varchar(max)][17]
  
Day 18: [Table-valued Parameters][18]
  
Day 19: [Filtered Indexes][19]
  
Day 20: [Indexes with Included Columns][20]
  
Day 21: [TRY CATCH][21]
  
Day 22: [Dynamic Management Views][22]
  
Day 23: [OBJECT_DEFINITION][23]
  
Day 24: [Index REBUILD and REORGANIZE][24]

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