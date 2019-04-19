---
title: 'SQL Advent 2012 Day 1: Sizing database files'
author: SQLDenis
type: post
date: 2012-12-01T08:42:00+00:00
ID: 1813
excerpt: This post shows how having a database that is not correctly sized will impact performance
url: /index.php/datamgmt/dbprogramming/sizing-database-files/
views:
  - 17088
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - filegroups
  - files
  - log
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - tempdb

---
This post will demonstrate that there is a difference in performance if you don't size your database file accordingly. It is a good practice to have your database sized correctly for the next 6 to 12 months, you don't want your server wasting cycles with growing files all the time. Figure out how big your files are now, figure out how much they will grow in the next year and size your files accordingly, check back every month or so to see if your estimates were correct. 

By default SQL Server will create databases wilt very small files when you create a database and you don't specify the sizes. If you have people creating databases on your servers, consider adding a DDL trigger to notify you when a new DB is added so that you can talk to the database creator and size the files. You also can change the defaults on the server so that you don't have the 10% growth either.

First let's see what the difference is when we have a database where the files will have to grow versus one where the files are big enough for the data that will be inserted.

Here we are creating two databases, one with much bigger files than the other one

This DB is correctly sized for the data that will be inserted

sql
CREATE DATABASE [TestBigger]
 ON  PRIMARY 
( NAME = N'TestBigger', FILENAME = N'f:TempTestBigger.mdf' , 
SIZE = 509600KB , FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'TestBigger_log', FILENAME = N'f:TempTestBigger_log.ldf' , 
SIZE = 502400KB , FILEGROWTH = 10%)
GO
```

This database is very small and will have to be expanded many times to accommodate all the data I will be inserting later on

sql
CREATE DATABASE [TestSmaller]
 ON  PRIMARY 
( NAME = N'TestSmaller', FILENAME = N'f:TempTestSmaller.mdf' , 
SIZE = 1280KB , FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'TestSmaller_log', FILENAME = N'f:TempTestSmaller_log.ldf' , 
SIZE = 504KB , FILEGROWTH = 10%)
GO
```

[edit]
  
_On sql server 2012, you might need to make the size of the 'TestSmaller file larger</p> 

If you get an error like the following

Msg 1803, Level 16, State 1, Line 1
  
The CREATE DATABASE statement failed. The primary file must be at least 4 MB to accommodate a copy of the model database.

Make the size of the primary file bigger, change the bold part from 1280KB to 5280KB or bigger if you still get the error

NAME = N'TestSmaller', FILENAME = N'f:TempTestSmaller.mdf' ,
  
SIZE = **1280KB** , FILEGROWTH = 1024KB</em>

[/edit]
  
These two stored proc calls are just to verify that the files match with what we specified, you can use sp_helpdb to check the size of a database that you created when you don't specify the file sizes

sql
EXEC sp_helpdb 'TestBigger'
```

<pre>name	        filename	            filegroup	SIZE
TestBigger	f:TempTestBigger.mdf	     PRIMARY	509632 KB
TestBigger_log	f:TempTestBigger_log.ldf	NULL	502400 KB</pre>



sql
EXEC sp_helpdb 'TestSmaller'
```

<pre>name	        filename	             filegroup	SIZE
TestSmaller	f:TempTestSmaller.mdf	     PRIMARY	1280 KB
TestSmaller_log	f:TempTestSmaller_log.ldf	NULL	 512 KB</pre>

Next, we are creating two identical tables, one in each database

sql
USE TestSmaller
GO
CREATE TABLE test (SomeName VARCHAR(100), SomeID VARCHAR(36), SomeOtherID VARCHAR(100), SomeDate DATETIME)
```

sql
USE TestBigger
GO
CREATE TABLE test (SomeName VARCHAR(100), SomeID VARCHAR(36), SomeOtherID VARCHAR(100), SomeDate DATETIME)
```

This query is just used so that the data is cached for the two inserts later on, this way the data doesn't have to be fatched from disk for either inserts, you can discard the results after the query is done

sql
USE master
GO


SELECT TOP 1000000 c1.name,NEWID(),NEWID(),GETDATE() 
FROM sys.sysobjects c1
CROSS JOIN sys.sysobjects c2
CROSS JOIN sys.sysobjects c3
CROSS JOIN sys.sysobjects c4
```

Here is the first insert into the bigger database

sql
INSERT TestBigger.dbo.test
SELECT TOP 1000000 c1.name,NEWID(),NEWID(),GETDATE() 
FROM sys.sysobjects c1
CROSS JOIN sys.sysobjects c2
CROSS JOIN sys.sysobjects c3
CROSS JOIN sys.sysobjects c4
```

Here is the second insert into the smaller database

sql
INSERT TestSmaller.dbo.test
SELECT TOP 1000000 c1.name,NEWID(),NEWID(),GETDATE() 
FROM sys.sysobjects c1
CROSS JOIN sys.sysobjects c2
CROSS JOIN sys.sysobjects c3
CROSS JOIN sys.sysobjects c4
```
On several machines I tested on, it takes half the time or less to insert the data in the bigger database compared to the smaller database. How about on your machine, do you see that the insert into the bigger database takes less than half the time it takes to insert into the smaller database?

Check the sizes of the databases again

sql
exec sp_helpdb 'TestBigger'
```

<pre>name	        filename	           filegroup	size
TestBigger	f:TempTestBigger.mdf	     PRIMARY	509632 KB
TestBigger_log	f:TempTestBigger_log.ldf	NULL	502400 KB</pre>



sql
exec sp_helpdb 'TestSmaller'
```

<pre>name	        filename	             filegroup	size
TestSmaller	f:TempTestSmaller.mdf	     PRIMARY	215296 KB
TestSmaller_log	f:TempTestSmaller_log.ldf	NULL	427392 KB</pre>

As you can see, the bigger database did not expand, the smaller database expanded a lot.

**Autogrow**
  
If you do use autogrow, then make sure you don't use the default 10%, take a look at this message

> Date 11/30/2012 12:57:56 PM
  
> Log SQL Server (Current – 11/25/2012 5:00:00 AM)
> 
> Source spid62
> 
> Message
  
> Autogrow of file 'MyDB_Log' in database 'MyDB' took 104381 milliseconds. Consider using ALTER DATABASE to set a smaller FILEGROWTH for this file.

See that, it took a long time, you don't want to grow a one terabyte file by ten percent, that would be one hundred gigabytes, that is huge. Use something smaller and don't use percent, the bigger the file gets the longer it will take to expand the file.

**File placement**
  
Separate the log files from the data files by placing them on separate hard drives. Placing the files on separate drives allows I/O activity to occur at the same time for both the data and log files. Instead of having huge files consider having smaller files in separate filegroups. Put different tables used in the same join queries in different filegroups as well. This will improve performance, because of parallel disk I/O searching for joined data.

Put heavily accessed tables and the nonclustered indexes that belong to those tables on different filegroups. This will improve performance, because of parallel I/O if the files are located on different physical disks. Just remember that you can't separate the clustered indexes from the base table, you can only do this for non clustered indexes. Of course people can get very creative, I have worked with a database once where each table was placed in its own filegroups, there were hundreds of files....what a mess

**Tempdb**
  
There are all kinds of recommendations about how many data files you should have for tempdb. Start with 4 files and add more files if you see contention. Paul Randal, has a detailed post here: [A SQL Server DBA myth a day: (12/30) tempdb should always have one data file per processor core][1].
  
If you can, place tempdb on its own physical drive as well, separated from the user databases.

**Test, test, test**
  
Never ever blindly follow what you read on the internet, make sure that you test it out first on a QA server before promoting the changes to production!!

That is all for day one of the SQL Advent 2012 series, come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][2]

 [1]: http://www.sqlskills.com/blogs/paul/post/A-SQL-Server-DBA-myth-a-day-(1230)-tempdb-should-always-have-one-data-file-per-processor-core.aspx
 [2]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap