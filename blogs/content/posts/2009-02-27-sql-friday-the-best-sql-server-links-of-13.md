---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 13
author: SQLDenis
type: post
date: 2009-02-27T12:23:52+00:00
ID: 335
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-13/
views:
  - 5561
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Time for another episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show.
  
Here is what I found interesting this past week in SQL Land:

**[Is CHECKDB A Necessity?][1]**
  
I'm often asked by professionals whether CheckDB is still recommended on SQL2k8. SQL is calculating a checksum to avoid physical page corruption since 2k5. However in my experience (starting with SQL 7.0) I’ve never been able to clearly say that CheckDB is not necessary .. Does anyone have a good advice? Read the post to find out the answer

**[Run-time Execution Plan Options][2]**
  
Joe Chang made a post and hes sks “What are the top core SQL Server engine performance issues today, after all the improvements that have gone into 2005 and 2008? (I am excluding matters beyond the power of Microsoft, like eliminating bad developers.)”

**[Enabling Data Compression By Default in a SQL Server 2008 Database][3]**
  
Aaron Alton show us how you could have every table in your database have data compression on by default. Of course he also warns you that this is not necessarily a good thing

**[SQL in the Cloud][4]** and **[SQL Server In The Cloud Vaporware Or Inevitable?][5]**
  
Two links dealing with SQL Server in the cloud and if it will happen, see also our poll here: **[Poll: Your Database in The Cloud?][6]**

**[Always Test Your Assertions – Calendar Tables vs. Table Valued Functions][7]**
  
Aaron Alton shows us performance differences between Calendar Tables and Table Valued Functions

And this week we have a bonus link, it is not SQL Server but MySQL, a very interesting post

**[How FriendFeed uses MySQL to store schema-less data][8]**
  
Bret Taylor writes: We use MySQL for storing all of the data in FriendFeed. Our database has grown a lot as our user base has grown. We now store over 250 million entries and a bunch of other data, from comments and “likes” to friend lists.

As our database has grown, we have tried to iteratively deal with the scaling issues that come with rapid growth. We did the typical things, like using read slaves and memcache to increase read throughput and sharding our database to improve write throughput. However, as we grew, scaling our existing features to accomodate more traffic turned out to be much less of an issue than adding new features.



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][9] forum or our [Microsoft SQL Server Admin][10] forum**<ins></ins>

 [1]: http://blogs.msdn.com/psssql/archive/2009/02/20/sql-server-is-checkdb-a-necessity.aspx
 [2]: http://sqlblog.com/blogs/joe_chang/archive/2009/02/21/run-time-execution-plan-options.aspx
 [3]: http://feedproxy.google.com/~r/TheHobt/~3/yC00L4Ps7o0/enabling-data-compression-by-default-in.html
 [4]: http://sqlblog.com/blogs/paul_nielsen/archive/2009/02/24/sql-in-the-cloud.aspx
 [5]: http://sqlblog.com/blogs/denis_gobo/archive/2009/02/25/12206.aspx
 [6]: http://forum.ltd.local/viewtopic.php?f=17&t=4736
 [7]: http://feedproxy.google.com/~r/TheHobt/~3/HuP8wH5GltI/always-test-your-assertions-calendar.html
 [8]: http://bret.appspot.com/entry/how-friendfeed-uses-mysql
 [9]: http://forum.ltd.local/viewforum.php?f=17
 [10]: http://forum.ltd.local/viewforum.php?f=22