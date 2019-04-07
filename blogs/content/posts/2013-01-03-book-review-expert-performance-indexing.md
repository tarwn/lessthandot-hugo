---
title: 'Book Review: Expert Performance Indexing for SQL Server 2012'
author: Jes Borland
type: post
date: 2013-01-03T13:47:00+00:00
ID: 1896
excerpt: I just finished reading Expert Performance Indexing for SQL Server 2012 by Jason Strate and Ted Krueger. This is the only index-specific book that I know of for SQL Server, and it was a long-overdue resource.
url: /index.php/itprofessionals/book-review/book-review-expert-performance-indexing/
views:
  - 11501
rp4wp_auto_linked:
  - 1
categories:
  - Book Review

---
I just finished reading [Expert Performance Indexing for SQL Server 2012][1] by Jason Strate ([blog][2] | [twitter][3]) and Ted Krueger ([blog][4] | [twitter][5]). This is the only index-specific book that I know of for SQL Server, and it was a long-overdue resource. <img style="float: right;" src="http://www.apress.com/media/catalog/product/cache/9/image/9df78eab33525d08d6e5fb8d27136e95/A/9/A9781430237419-3d_3.png" alt="" />

The progression of the book is very logical, from index fundamentals to special types of indexes, index maintenance to a method to analyze and implement changes. The examples and queries that are included are thorough yet understandable. You are given a solid foundation as to why you should do something, and the tools with which to do it. Another outstanding benefit to this book is that Jason and Ted have taken the knowledge gained in their years of working with business users and distilled that. This book goes beyond the purely technical reasons for doing something and encourages you to think about the impact to the business, the applications, and the users behind the databases.

The topics that are covered include:

**Index Fundamentals**

The foundation is covered here – heaps, clustered, and nonclustered indexes. Variations such as primary keys, included columns, and filtered indexes are discussed. The CREATE, ALTER, and DROP INDEX statements are broken down.

**Index Storage Fundamentals**

Understanding how SQL Server stores data and index pages will help you understand other concepts. Viewing information on pages by using DBCC IND and DBCC PAGE is covered. Then, the problem of fragmentation and how it occurs is discussed.

**Index Statistics**

This chapter thoroughly covers how to view the statistics associated with an index. DBCC SHOW\_STATISTICS, sys.dm\_db\_index\_usage\_stats, sys.dm\_db\_index\_operational\_stats, and sys.dm\_db\_index\_physical_stats are broken down.

**XML, Spatial, and Full-Text Indexing**

There is an overview of three not-as-common index types here – XML, spatial, and full text. The chapter discusses how each is created, and the effect on queries against that type of data.

**Index Myths and Best Practices**

There are a lot of misconceptions about how to use indexes in SQL Server. Myths such as “primary keys are always clustered” and “fill factor is applied to indexes during inserts” are broken down in this chapter. It’s a great look at how these things actually work. Best practices for indexes are also covered in this chapter.

**Index Maintenance**

How fragmentation occurs, and how to fix it, is covered in detail. Defragmentation can occur through several methods – index rebuild, index reorganization, drop and rebuild &#8211; and each of them is broken down.

**Indexing Tools**

Taking care of the existing indexes in your database isn’t enough. You’ll need to add new indexes over time, as your data and applications change. SQL Server offers two main tools to help you determine what indexes to add – the missing index DMOs and the Database Tuning Advisor (DTA). I admit that I’ve been skeptical of DTA up to this point, but I think I’ll give it another try soon, now that I have more information on the best way to use it.

**Index Strategies**

This chapter covers how to spot patterns in your data to help identify potential indexes. For example, there are valid reasons to keep a table a heap – those are discussed here. Using multiple column or GUID clustered indexes are two more topics. Various nonclustered index strategies such as building it with multiple columns or adding a filter are discussed. The best part of this chapter is that a query is run, the statistics are shown, an index is applied, and statistics are shown again – showing the differences in the execution plan. This is incredibly helpful.

**Query Strategies**

Adding or removing indexes isn’t the only way to improve performance in the database. Sometimes, modifying your queries to take advantage of indexes can do just as much good. Strategies such as using LIKE, concatenation, and computed columns – and how they use or don’t use indexes – are broken down in this chapter. This is a chapter I wish everyone that writes T-SQL would read!

**Index Analysis**

The final chapter is about putting it all together. A three-step process of monitoring, analyzing, and implementing is discussed in detail, with more examples.

### 5 of 5 Stars

This book is already one of the most valuable resources in my library. I know it will be referenced frequently (it already has been, and it’s littered with Post-It flags). I recommend anyone that works with SQL Server – whether as a developer, a DBA, or any combination thereof – pick up this book and read it. You don’t have to be a SQL Server expert to pick up this book and learn from it, and even experienced users will learn something new and interesting. You won’t find a more comprehensive index resource anywhere.

 [1]: http://www.apress.com/9781430237419
 [2]: http://www.jasonstrate.com/
 [3]: https://twitter.com/StrateSQL
 [4]: /index.php?disp=authdir&author=68
 [5]: https://twitter.com/onpnt