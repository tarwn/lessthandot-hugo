---
title: 'SQL Advent 2012 Day 10: SQL Server Maintenance'
author: SQLDenis
type: post
date: 2012-12-10T14:50:00+00:00
ID: 1840
excerpt: |
  This is day ten of the SQL Advent 2012 series of blog posts. Today we are going to look at SQL Server maintenance
  
  
  
    
  That is all for day tenof the SQL Advent 2012 series, come back tomorrow for the next one, you can also check out all the posts f&hellip;
url: /index.php/webdev/business-intelligence/sql-server-maintenance/
views:
  - 25495
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
tags:
  - checkdb
  - fragmentation
  - maintenance
  - sql server 2008
  - sql server 2012

---
This is day ten of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at SQL Server maintenance
  
[<img src="http://farm7.staticflickr.com/6075/6052402695_6b30a5ea57_m.jpg" width="174" alt="gear_shape"style="float:left;margin:0 5px 0 0;" />][2]

Just like with a car or a house, you need to do maintenance on databases as well. SQL Server has gotten better over the years, there are less knobs you need to turn out of the box but maintenance is still required.
  
In this post I will be looking at some stuff that you need to be aware of. Some of the things I will mention can be thought of as maintenance as well as regular checks. Think of a DBA as a car mechanic, instead of an oil change, tune up or checking the tire pressure, the DBA will check index fragmentation, run DBCC CHECKDB and make sure you have enough space for the database to grow for the next predetermined period. The things I will cover in this post are: fragmentation of indexes, free drives space, space in filegroups, running DBCC CHECKDB and finally making sure that you have the latest source code of your objects in a source control system.

## Check fragmentation of indexes

A lot of time your index will get fragmented over time if you do a lot of updates or inserts and deletes.
  
We will look at an example by creating a table, fragmenting the heck out of it and then doing a reorganize and rebuild on the index. I already wrote a blog post that shows this, you can find that post here: [Finding Fragmentation Of An Index And Fixing It][3]

Now instead of rolling your own solution, I mentioned a couple of solutions that already exist in the [Reinventing the wheel][4] post from yesterday. Take a look at [SQL Server Index and Statistics Maintenance][5] by [Ola Hallengren][6]

Also check out this index defrag script by [Michelle Ufford][7] [Index Defrag Script, v4.1][8]

[SQL Sentry Fragmentation Manager][9] is another option, this tool is not free but does additional things like how many concurrent operations can run.

## Check that your database is healthy by running DBCC CHECKDB

What does DBCC CHECKDB do? Here is the explanation from [Books On Line][10]
  
_Checks the logical and physical integrity of all the objects in the specified database by performing the following operations:</p> 

Runs DBCC CHECKALLOC on the database.

Runs DBCC CHECKTABLE on every table and view in the database.

Runs DBCC CHECKCATALOG on the database.

Validates the contents of every indexed view in the database.

Validates link-level consistency between table metadata and file system directories and files when storing varbinary(max) data in the file system using FILESTREAM.

Validates the Service Broker data in the database.</em>

So how frequent should you be running DBCC CHECKDB? Ideally you should be running DBCC CHECKDB as frequent as possible, do you want to find out that there is corruption when it is very difficult to fix since two weeks have passed or do you want to find out the same day so that you can fix the table immediately.

Paul Randal who worked on DBCC CHECKDB has a whole bunch of blog posts about DBCC CHECKDB, the posts can be found here http://www.sqlskills.com/blogs/paul/category/checkdb-from-every-angle.aspx

## Make sure that you have enough space left on the drives

Running out of space on a drive is not fun stuff, suddenly you can't insert any more data into your tables because no new pages can be allocated. If you have tools in your shop like cacti then this is probably already monitored. If you don't have any tools then either get a tool or roll your own. Here is how you can get the free space fo the drives with T-SQL

```sql
CREATE TABLE #FixedDrives(Drive CHAR(1),MBFree INT)

INSERT #FixedDrives
EXEC xp_fixeddrives

SELECT * FROM #FixedDrives
```

Here is the output for one of my servers

<pre>Drive	MBFree
------------------
C	6916  -- System
D	28921 -- Apps
L	52403 -- Log
M	4962  -- System databases
T	86208 -- Temps
U	71075 -- User databases 
V	212075-- User databases </pre>

Here is a simple way of using T-SQL to create a SQL Agent job that runs every 10 minutes and will send an email if you go below the threshold that you specified. This code is very simple and is just to show you that you can do this in T-SQL. You can make it more dynamic/configurable by not hardcoding the drives or thresholds

```sql
DECLARE @MBFreeD INT
DECLARE @MBFreeE INT
CREATE TABLE #FixedDrives(Drive CHAR(1),MBFree INT)

INSERT #FixedDrives
EXEC xp_fixeddrives

SELECT @MBFreeD =  MBFree
FROM #FixedDrives
WHERE DRIVE = 'D'

SELECT @MBFreeE =  MBFree
FROM #FixedDrives
WHERE DRIVE = 'E'


DROP TABLE #FixedDrives

IF @MBFreeD < 30000 OR @MBFreeE < 10000
BEGIN
      DECLARE @Recipients VARCHAR(8000)
	  SELECT @Recipients ='SomeGroup@SomeEmail.com'
		     
		DECLARE @p_body AS NVARCHAR(MAX), @p_subject AS NVARCHAR(MAX), @p_profile_name AS NVARCHAR(MAX)

		SET @p_subject = @@SERVERNAME + N'  Drive Space is running low'
		SET @p_body = ' Drive Space is running low <br><br><br>' + CHAR(13) + CHAR(10) + 'Drive D has ' 
		+ CONVERT(VARCHAR(20),@MBFreeD) + ' MB left <br>' + CHAR(13) + CHAR(10) + 'Drive E has ' 
		+ CONVERT(VARCHAR(20),@MBFreeE) + ' MB left'

		EXEC msdb.dbo.sp_send_dbmail
		   @recipients = @Recipients,
		   @body = @p_body,
		   @body_format = 'HTML',
		   @subject = @p_subject
END
```
## Make sure that you have enough space left for the filegroups

In the [Sizing database files][11] I talked about the importance of sizing database files. Just like you can run out of hard drive space, you can also fill up a file used by SQL Server. here is query that will tell you how big the file is, how much space is use and how much free space is left. You can use a query like this to alert you before you run out of space

```sql
SELECT
	a.FILEID,
	[FILE_SIZE_MB] = 
		CONVERT(DECIMAL(12,2),ROUND(a.size/128.000,2)),
	[SPACE_USED_MB] =
		CONVERT(DECIMAL(12,2),ROUND(FILEPROPERTY(a.name,'SpaceUsed')/128.000,2)),
	[FREE_SPACE_MB] =
		CONVERT(DECIMAL(12,2),ROUND((a.size-FILEPROPERTY(a.name,'SpaceUsed'))/128.000,2)) ,
	NAME = LEFT(a.NAME,35),
	FILENAME = LEFT(a.FILENAME,60)
FROM
	dbo.sysfiles a
```

## Have the latest scripts of all your objects

You might say that you have all the code for your objects in the database. What if you want to go back to the version of the proc from 3 days ago, is it really easier to restore a 800 GB backup from 3 days ago just to get the stored proc code? Of course not, make sure that you have DDL scripts of every object in source control, your life will be much easier.

I only touched on a couple of points here, some of the things mentioned here will also show up in the proactive notifications post in a couple of days. There is much more to maintenance than this, keep informed and make sure you understand what needs to be done.

That is all for day ten of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][12]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: http://www.flickr.com/photos/43830351@N03/6052402695/ "gear_shape by chance.press, on Flickr"
 [3]: /index.php/DataMgmt/DataDesign/finding-fragmentation-of-an-index-and-fi
 [4]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2012-day-10
 [5]: http://ola.hallengren.com/sql-server-index-and-statistics-maintenance.html
 [6]: http://ola.hallengren.com/
 [7]: http://sqlfool.com/
 [8]: http://sqlfool.com/2011/06/index-defrag-script-v4-1/
 [9]: http://www.sqlsentry.net/fragmentation-manager/sql-server-index-analysis-and-defrag.asp
 [10]: http://msdn.microsoft.com/en-us/library/ms176064
 [11]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sizing-database-files
 [12]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap