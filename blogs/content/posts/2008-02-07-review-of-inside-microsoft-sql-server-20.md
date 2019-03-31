---
title: Review of Inside Microsoft SQL Server 2005 Query Tuning and Optimization
author: SQLDenis
type: post
date: 2008-02-08T02:04:36+00:00
url: /index.php/datamgmt/datadesign/review-of-inside-microsoft-sql-server-20/
views:
  - 6603
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server Admin
tags:
  - book
  - databases
  - programming
  - review
  - sql

---
SQL performance tuning is probably one of those things you can do to really make a HUGE difference in performance. Let’s put this in perspective: take a typical application, if you can improve the performance by 100% then you really made a huge improvement. You can improve a SQL query by 1000% with 2 lines of code (sometimes all you have to do is take away a % sign). If you can make a query sargable so that the optimizer can do an index seek instead of an index scan your query might go from 12 seconds to 200 milliseconds. Now try doing that in an application, even if you change all the string concatenation to use a stringbuilder instead of creating new strings all the time you will not get such a drastic performance improvement. I am sure you get the point by now, let’s talk about the book. 

[Inside Microsoft SQL Server 2005: Query Tuning and Optimization][1] is part 4 of the Inside Microsoft SQL Server 2005 series, it is written by Kalen Delaney and five other authors. There are 6 chapters in this book



1 A Performance Troubleshooting Methodology
  
This chapter explains some typical things that affect performance and also gives a troubleshooting overview

2 Tracing and Profiling
  
This chapter explains how to use the profiler and how to analyze traces. SQL Server’s built-in traces are also covered

3 Query Execution
  
This chapter gives a query processing and execution overview. It explains how to read plans and goes into a lot of detail about analyzing plans

4 Troubleshooting Query Performance
  
This chapter explains how to detect problems in plans, how to improve queries and some best practices

5 Plan Caching and Recompilation
  
This chapter goes into detail about plan caching and recompilation and how to troubleshoot plan cache issues

6 Concurrency Problems
  
The final chapter deals with concurrency (locking, blocking and deadlocking)

This is an excellent book for an intermediate/advanced developer. There is so much new stuff in SQL Server 2005 compared to 2000 to help you with tuning queries that you probably want to read each chapter several times. The Dynamic Management Views are a big help and this book shows you how to use them. Some other cool stuff in this book is the discussion of internal tables, undocumented DBCC commands and undocumented trace flags to discover information which could help you determine much faster what the cause of a performance problem might be. Some pages are packed with so much information that you need to pause for a second and process all that info (I have read some pages two to three times in a row). You will also find out that there are more joins besides left, full and outer. Page 137 for example has a nice table with the three Physical Join Operators: Nested Loop Join, Hash Join and Merge Join. This table lists the characteristics for each of these joins.



If you are an intermediate to advanced developer then I highly recommend this book. 



I have interviewed Kalen a while back about this book and you can find that interview here: [Interview With Kalen Delaney About Inside Microsoft SQL Server 2005 Query Tuning and Optimization][2]

 [1]: http://www.amazon.com/gp/product/0735621969/102-5735017-0910517?ie=UTF8&tag=sql08-20&linkCode=xm2&camp=1789&creativeASIN=0735621969
 [2]: http://sqlservercode.blogspot.com/2007/10/interview-with-kalen-delaney-about.html