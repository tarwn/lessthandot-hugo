---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 9
author: SQLDenis
type: post
date: 2009-01-30T10:11:30+00:00
ID: 305
url: /index.php/webdev/webdesigngraphicsstyling/sql-friday-the-best-sql-server-links-of-9/
views:
  - 3197
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling

---
Here we are with another juicy episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show
  
Here is what I found interesting this past week in SQL Land:

**[Denormalizing to enforce business rules: Running Totals][1]**
  
Calculating running totals is notoriously slow, whether you do it with a cursor or with a triangular join. It is very tempting to denormalize, to store running totals in a column, especially if you select it frequently.

**[Creating a 60 GB Index][2]**
  
Recently, I needed to create an index on a 1.5 billion row table. I’ve created some large indexes before, but this was the largest, so I thought I’d share my experience in case anyone was interested

**[Back To Basics: What are indexes and what are they used for?][3]**
  
A while back someone posted on the ITKE forum asking what Indexes where, and what they were used for. I put up a quick answer, but I felt that it deserved a more in depth blog post; so here it is.

In the basic view, an Index is a subset of columns from a table. This subset of columns is stored in a sorted order so that the SQL Server can more quickly find the records based on the data in the index.

**[New Vendor Interview with an “annoying” DBA][4]**
  
Once you have been a DBA for any length of time, you will encounter the situation of a new vendor coming in with software that has a database back end. Wherever I am working or helping, I like to interject the idea of “DBA Input” as early into the process as possible and I go through a discussion with them.

**[When did DBCC CHECKDB last run on my databases?][5]**
  
I've seen this question a few times on the forums, and unfortunately there never is a really good answer that is easy to use. SQL Server 2000 didn't track this information internally, and even though an internal tracking mechanism was added to SQL Server 2005, it isn't the easiest thing to get for every database in a large environment

**[Hot It Works: SQL Server SuperLatch'ing / Sub-latches][6]**
  
SQL Server Books Online uses the terms super latches and sub-latch to describe them. For example the SQL Server:Latches performance counter group calls them super latches. The DVM that exposes the super latches use the term sub-latch, sys.dm\_os\_sublatches. They are the same internal structure simply exposed under separate terms.

**[SQL Server Certification Statistics][7]**
  
Interesting post make sure you read the comments

**[The IDENTITY Property: A Much-Maligned Construct in SQL Server][8]**
  
I’d be willing to place a bet that on any given week, I’ll field at least one question on the MSDN Forums that reads a bit like this:

“I need to create a unique key for a table using a Stored Procedure|User-Defined Function. Please do NOT suggest IDENTITY.”



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][9] forum or our [Microsoft SQL Server Admin][10] forum**<ins></ins>

 [1]: http://sqlblog.com/blogs/alexander_kuznetsov/archive/2009/01/23/denormalizing-to-enforce-business-rules-running-totals.aspx
 [2]: http://sqlfool.com/2009/01/creating-a-60-gb-index/
 [3]: http://itknowledgeexchange.techtarget.com/sql-server/back-to-basics-what-are-indexes-and-what-are-they-used-for/
 [4]: http://www.straightpathsql.com/blog/2009/1/27/new-vendor-interview-with-an-annoying-dba.html
 [5]: http://sqlblog.com/blogs/jonathan_kehayias/archive/2009/01/28/when-did-dbcc-checkdb-last-run-on-my-databases.aspx
 [6]: http://blogs.msdn.com/psssql/archive/2009/01/28/hot-it-works-sql-server-superlatch-ing-sub-latches.aspx
 [7]: http://sqlblog.com/blogs/greg_low/archive/2009/01/29/sql-server-certification-statistics.aspx
 [8]: http://feedproxy.google.com/~r/TheHobt/~3/nqVWQTDVS6s/identity-property-much-maligned.html
 [9]: http://forum.ltd.local/viewforum.php?f=17
 [10]: http://forum.ltd.local/viewforum.php?f=22