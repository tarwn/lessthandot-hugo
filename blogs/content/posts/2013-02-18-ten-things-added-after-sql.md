---
title: Ten things added after SQL Server 2000 that I like the most
author: SQLDenis
type: post
date: 2013-02-18T13:10:00+00:00
ID: 2005
excerpt: 'It is almost 8 years since SQL Server 2005 has been released, we have gotten 3 major versions since SQL Server 2000: SQL Server 2005, SQL Server 2008 and SQL Server 2012. I decided to take a look to see what I find the most useful that has been added af&hellip;'
url: /index.php/datamgmt/dbprogramming/ten-things-added-after-sql/
views:
  - 19908
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms

---
It is almost 8 years since SQL Server 2005 has been released, we have gotten 3 major versions since SQL Server 2000: SQL Server 2005, SQL Server 2008 and SQL Server 2012. I decided to take a look to see what I find the most useful that has been added after SQL Server 2000. Since I already wrote blog posts about most of the things I listed I decided to just give a small description why I like the feature followed by a link to the blog post I already created earlier.

Here are the ten things I like the most

* Date and time data types
	  
* Compression
	  
* Windowing functions
	  
* Partitioning
	  
* Schemas
	  
* Sequences
	  
* DDL Triggers
	  
* Try catch
	  
* Varchar max
	  
* DMVs

Let&#8217;s take a look into some more detail why I like these features.

**Date and time data types**
  
I was very glad when this was added to SQL Server 2008. I deal with a lot of data and the ability to use date which is 3 bytes compared to datetime which is 8 bytes saves me a lot of space. When you have a billion rows you save 5 GB right there. Now that you can store time separately you get better performing queries that result in seeks instead of scans. More about date and time can be found in this post: [Date and time][1]

**Compression**
  
Data compression was added in SQL Server 2008 as well. I use data compression mostly for archive tables and tables that are read from more than they are written to. The space savings can be dramatic, this is especially true if the compound clustered index is sorted in such a way that the values are the same for most of the columns. I once saved about 140 GB by compressing 8 or 9 tables over the weekend

**Windowing functions**
  
The windowing functions row\_number, rank and dense\_rank are being used all over the place now. This was added in SQL Server 2005 and at least now I don&#8217;t have to look up the syntax every time I want to use them. This simplified a lot for me, no more running counts and using temp tables with identity columns to provide a row number. More about windowing functions can be found here: [Windowing Functions][2]

**Partitioning**
  
I have done partitioning even on SQL Server 2000 with partitioned views, native partitioning makes this much easier from a maintenance perspective. More about partitioning can be found here: [Partitioning][3]

**Schemas**
  
Schemas were introduced in SQL Server 2005. I like to use schemas to organize objects in a way that makes sense and is intuitive. Schemas also give you the ability to give users access to a set of object without having to specify each object separately. More about schemas can be found here: [Schemas][4]

**Sequences**
  
This has been on the SQL Server wish list (remember the email address before they had connect?) forever. Instead of the ghetto one identity table for the whole database method, you can now have sequences that are more flexible than previous methods. More about sequences can be found here: [A first look at sequences in SQL Server Denali][5]

**DDL Triggers**
  
When you have developers telling you that columns disappear or that columns have changed, throw a DDL trigger for ALTER TABLE on your DB and find out exactly what is going on. Once I did that and told the developer that I created a DDL trigger all his problems mysteriously never happened again. More about DDL trigger can be found here [DDL Triggers][6] and here [Use DDL Triggers To Track Table Changes][7]

**Try catch**
  
Error handling was very primitive until SQL Server 2005. You had to deal with error codes and there was no intuitive way of getting an error message back. More can be found here: [TRY CATCH][8]

**Varchar max**
  
If you ever worked with text and ntext you know what a pain in the neck those data types are. A whole bunch of functions don&#8217;t work and there are tons of other restrictions as well. The (max) datatypes makes this much more manageable and easier than before. More can be found here: [varchar(max)][9]

**DMVs**
  
I am a big fan of the dynamic management views, there is no need to run all kinds of DBCC commands, stored procedures, traces etc etc to get some information that you are interested in. The dynamic management views make this easier than ever and in every release we get more and more of them. More can be found here: [Dynamic Management Views][10]

* * *</p> 

## Ten other additions I also like

Some of these should be in the top ten list but since I can only fit ten items they got pushed into this list. Pivot, mirroring and backup compression are things that I use daily and very grateful for

* Mirroring
	  
* Pivot and unpivot
	  
* Columnstore indexes
	  
* Table valued parameters
	  
* Common table expressions
	  
* IIF
	  
* Backup compression
	  
* OFFSET N ROWS FETCH NEXT N ROWS ONLY
	  
* Object_definition
	  
* Compound Operators

**Mirroring** 
  
Mirroring was added to SQl Server 2005 Service Pack 1, it was also in the RTM version but you had to use a trace flag to enable it. We used to do a lot of replication but have replaced almost all of it with mirroring. We also take snapshots on the mirrored server so that reporting can still be run off that server instead of it being a dead weight

**Pivot and unpivot**
  
Pivot is a nice addition but if you don&#8217;t know the columns beforehand you will need to use dynamic SQL. I still have to lookup pivot and unpivot every time I use them since the syntax is a little complex. More can be found here [Crosstab with PIVOT][11] and [UNPIVOT][12]

**Columnstore indexes**
  
Having being exposed to the concept of a columnar database with Sybase IQ, I knew what this was before it came out. It is a nice feature but there are still a tons of restrictions in the current release, it is read only and some data types are not supported

**Table Value Constructor**
  
Table Value Constructors enable you to pass in a bunch of values in a single DML statement instead of a bunch of union statements. More about Table Value Constructors can be found here: [Table Value Constructor][13]

**Common table expressions**
  
Common Table Expressions (you will see them called CTE also) were introduced in SQL Server 2005 and you can think of them as a derived table in another form of a table expression. Using CTEs makes for less cluttered code and CTEs can be manipulated as regular tables as well. More about Common Table Expressions can be found here [Common Table Expressions][14] and here [Use common table expressions to simplify your updates in SQL Server][15]

**IIF**
  
IIF is just syntactic sugar for a case statement but if you come from other languages like vb or Excel this might feel more natural to you. More can be found here: [IIF][16]

**Backup compression**
  
I have databases in the TB+ size, taking a backup which is a fourth of the size makes it more manageable and save time and money in terms of storage and moving the backup around. You can find more info here: [Testing backup compression in SQL Server 2008][17]

**OFFSET N ROWS FETCH NEXT N ROWS ONLY**
  
This is a welcome addition to simplify paging code. Read more here: [Using OFFSET N ROWS FETCH NEXT N ROWS ONLY In SQL Server for easy paging][18]

**Object_definition**
  
I use the object\_definition function all the time. If I need to see in how many stored procedures a table is used I can use the object\_definition function to find that out very easy. More info here: [OBJECT_DEFINITION][19]

 **Compound Operators**
  
Compound Operators make it possible to declare and assign a value at the same time, for example `declare @i int =5;` This makes for compact code and you don&#8217;t have to search where the variable is initialized. More information can be found here: [Compound Operators Or How T-SQL Is Morphing Into VB Or C#][20]

* * *

<div class="image_block">
  <a href="/wp-content/uploads/users/SQLDenis/BillyMase.PNG?mtime=1361730496"><img alt="" src="/wp-content/uploads/users/SQLDenis/BillyMase.PNG?mtime=1361730496" width="397" height="396" /></a>
</div>



I could go on and on about even more stuff I like, I am just going to list 15 or so more things infomercial style&#8230;.. 

SSMS
  
SSIS
  
Fast recovery
  
Page restores
  
Online Index Rebuild
  
Except and Intersect
  
Filtered Indexes
  
Indexes with included columns
  
CROSS APPLY
  
Dynamic TOP
  
MERGE statement
  
OUTPUT clause
  
Optimize for ad hoc workloads
  
Snapshot Isolation

Now let&#8217;s hear it from you, what do you like the most that they have added since SQL Server 2005? I wonder how difficult it would be to pick out the DBAs from the developers based on the items people list 🙂

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-1
 [2]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-6
 [3]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-3
 [4]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-4
 [5]: /index.php/DataMgmt/DataDesign/a-first-look-at-sequences-in-sql-server
 [6]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-13
 [7]: /index.php/DataMgmt/DBProgramming/MSSQLServer/use-ddl-triggers-to-track
 [8]: /index.php/DataMgmt/DBProgramming/MSSQLServer/try-catch-sql-advent-2011
 [9]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-17
 [10]: /index.php/DataMgmt/DataDesign/dynamic-management-views
 [11]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-7
 [12]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-8
 [13]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-12
 [14]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-5
 [15]: /index.php/DataMgmt/DBProgramming/MSSQLServer/use-common-table-expressions-to-simplify-your-updates-in-sql-server
 [16]: /index.php/DataMgmt/DBProgramming/MSSQLServer/a-quick-look-at-the
 [17]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/testing-backup-compression-in-sql-server-2008
 [18]: /index.php/DataMgmt/DBProgramming/MSSQLServer/using-offset-n-rows-fetch-next-n-rows-on
 [19]: /index.php/DataMgmt/DBProgramming/MSSQLServer/object_definition-sql-advent-2011-day
 [20]: /index.php/DataMgmt/DataDesign/compound-operators-or-how-t-sql-is-morph