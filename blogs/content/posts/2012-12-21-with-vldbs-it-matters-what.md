---
title: 'SQL Advent 2012 Day 21: With VLDBs it matters what you do and how you do it'
author: SQLDenis
type: post
date: 2012-12-22T00:12:00+00:00
ID: 1876
excerpt: This is day twenty-one of the SQL Advent 2012 series of blog posts. Today we are going to look at Very Large Databases
url: /index.php/datamgmt/dbprogramming/with-vldbs-it-matters-what/
views:
  - 11395
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - indexing
  - performance
  - query
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - very large databases
  - vldb

---
This is day twenty-one of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at why it matters how you do things when working with a VLDB 

**VLD what?**
  
VLDB stands for Very Large Database, Not too long ago the definition of VLDB was a database that occupies more than 1 terabyte or contains several billion rows. This of course will change over time, there are quite a few companies with Petabyte size databases. Servers with many CPUs and lots of RAM are required when your databases are big

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SQLServerCPUandRAM.jpg?mtime=1356141722"><img alt="I like big databases and I cannot lie" title ="I like big databases and I cannot lie" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SQLServerCPUandRAM.jpg?mtime=1356141722" width="582" height="395" /></a>
</div>

**What is the big deal with VLDB anyway, it is just bigger right?**
  
The problem with a VLDB is that you have to change your mindset and you have to change your ways how you do certain things. Think of it like driving 20 miles per hours compared to driving 160 miles per hour, when you drive very fast you can&#8217;t get away with doing stupid stuff on the road, you will crash. The same is true when working with big databases. You can&#8217;t just delete 100 million rows, you might fill up the log file, you have to do it in batches if you can&#8217;t use a truncate statement.

**Storage**
  
While you can get away with having just one drive when dealing with smaller database, this doesn&#8217;t hold true for Very Large Databases. With Very Large Databases, ideally you want separate drives for tempdb, log file and data files. You can also put the non clustered indexes on a different spindle, separate from the heaps and clustered indexes. Also make sure that you [size your database files][2] correctly to improve performance.
  
If you have 64 GB of RAM and your database is 50 GB, it is very likely that the whole database will be in RAM at some point. When your database is 2 TB and you have only 512 GB of RAM, you cannot even have a quarter of the DB in RAM. This is where you need to have fast hard drives. A fast SAN or some Solid State Drives are worth looking into.

**Partitioning**
  
Partitioning can help with maintenance and returning data faster, take a look at my [partitioning][3] post for more info

**Indexes**
  
Make the indexes narrow, you want your lookups to be as efficient as possible.
  
If using partitions you can now rebuild just one partition of the index, this will make index maintenance easier and faster.

**Deletes**
  
When you have a small database, you can delete all the rows from a table without a problem generally. Every now and then you will have someone do this, they will of course do this just one because after that you will have _THE TALK_ with them about this

sql
DELETE HugeTable
```

Instead of doing that, use truncate or do deletes in batches of 50000 for example

**Select * from HugeTable**
  
Every now and then you will have someone execute something like the following

sql
SELECT * 
FROM HugeTable
ORDER By SomeColumn
```
When doing something like that SQL Server will create a worktable in tempdb, if the table is big and your tempdb is placed on a drive that doesn&#8217;t have a lot of space, you will run out of space, take a look at [Dealing with the could not allocate new page for database &#8216;TEMPDB&#8217;. There are no more pages available in filegroup DEFAULT error message][4] how to resolve this

**Compression**
  
Compression is great, I use it, it makes the backups smaller, it makes the restore faster. I use database compression as well as data compression, when using data compression, SQL Server will be able to store more data per page and thus you will be able to have more data in RAM

**Testbed size**
  
When coding against Very Large Databases, you need to test with a QA or testbox that has about the same data, you will get into trouble if you don&#8217;t. Take a look at [Your testbed has to have the same volume of data as on production in order to simulate normal usage][5] to see what can happen.

**Crappy queries**
  
Ah yes, how to bring the database to its knees, have some n00bs write some queries against your database. While you can get away with writing non-[SARGable queries][6], queries where the index is not used, you will suffer immensely if you do this on Very Large Databases

I only touched upon a couple of key points, just keep in mind that if you do the thing I mentioned here even with smaller databases, you won&#8217;t suffer when your database starts to grow. And no, while premature optimization might be the root of all evil, I would call this best practices instead

That is all for day twenty-one of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][7]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sizing-database-files
 [3]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-3
 [4]: /index.php/DataMgmt/DataDesign/could-not-allocate-new-page-for-database
 [5]: /index.php/DataMgmt/DataDesign/your-testbed-has-to-have-the-same-volume
 [6]: /index.php/DataMgmt/DBProgramming/sargable-queries
 [7]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap