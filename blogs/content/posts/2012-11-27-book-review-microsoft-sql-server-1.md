---
title: 'Book Review: Microsoft SQL Server 2012 T-SQL Fundamentals'
author: Axel Achten (axel8s)
type: post
date: 2012-11-27T17:37:00+00:00
ID: 1704
excerpt: "With a basis as a system engineer, I became a DBA. And DBA's don't often write real DML queries. I can read a query, understand what it's doing but when I need to read one I need BOL the help me out. Taking the 070-457 Transition Your MCTS on SQL Server&hellip;"
url: /index.php/datamgmt/dbprogramming/book-review-microsoft-sql-server-1/
views:
  - 13857
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - book review
  - microsoft press
  - sql server 2012
  - t-sql

---
With a basis as a system engineer, I became a DBA. And DBA's don't often write real DML queries. I can read a query, understand what it's doing but when I need to read one I need BOL the help me out. Taking the [070-457 Transition Your MCTS on SQL Server to MCSA: SQL Server 2012,Part 1][1] exam showed me I'm strong in database administration but there was room for improvement for my query skills. That's why I bought [Microsoft SQL Server 2012 T-SQL Fundamentals][2] by Itzik Ben-Gan ([blog][3] | [twitter][4]).

<div class="image_block">
  <a href="http://shop.oreilly.com/product/0790145321978.do"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/BRTsql.jpg?mtime=1354044858" width="175" height="214" /></a>
</div>

**Chapter 1: Background to T-SQL Querying and Programming**
  
When you followed a bunch of trainings and read some books about SQL Server, you will possibly have seen some chapters on the origin and basics of relational databases and the SQL language. But I bet you have never seen a chapter like this one. In a straight and clear way Itzik guides you through the mathematics behind SQL, the flavors and architecture of SQL Server and the definitions of tables en data integrity. Now you're ready to stand between other professionals and at least know what they are talking about. For me, they should include these 25 pages in every SQL Server learning book.

**Chapter 2: Single-Table Queries**
  
In the largest chapter of this book, the writer explains every part of the SELECT statement and how these queries are processed by SQL Server. It's the basis a user needs to start understanding set-based logic. In the second part of the chapter predicates and operators, CASE expressions, NULL handling, character data, date and time data and even metadata is handled. It might seem overwhelming for starters but all the essentials for writing queries are in this chapter.

**Chapter 3: Joins**
  
I thought I knew how joins work, but after this chapter I know a lot more. All join types are explained in detail including a subchapter Beyond the Fundamentals. And again explained in such a clear way. My students will certainly benefit from this.

**Chapter 4: Subqueries**
  
The chapter starts with the self-contained subqueries, that are easy to understand and moves on to the correlated subqueries and shows the power of them. The EXISTS predicate is also well explained. In the beyond the fundamentals of the chapter some examples are shown on how to build windowing functionality without the windowing functions. The only disadvantage in the chapter is the fact that SOME, ANY and ALL are not covered because they are rarely used. From experience I know they might show up on Microsoft exams.

**Chapter 5: Table Expressions**
  
A beautiful chapter where you will learn to write real powerful queries using derived tables, Common Table Expressions, Views , Functions and the APPLY operator. You'll see the true value of these expressions when you see the complex definitions of the exercises and the simple queries you need in the solution.

**Chapter 6: Set Operators**
  
UNION, INTERSECT, EXCEPT will have no more secrets for you, the writer even shows you how to create queries to implement the INTERSECT and EXCEPT ALL operators that are supported in Standard SQL but are not implemented in T-SQL.

**Chapter 7: Beyond the Fundamentals of Querying**
  
Everybody has heard of the new Windowing functions in SQL 2012 but little people can explain it as clear and simple as it's written down in this book. Also pivoting is explained again and all the possibilities of groupings and grouping sets are written out in plain T-SQL and with use of the functions so you can really see what the different key words are doing. So the chapter goes indeed beyond the fundamentals, but I would mark it as one of the essentials you should learn about T-SQL

**Chapter 8: Data Modification**
  
The basics of INSERT, UPDATE, DELETE and MERGE, BULK INSERT, Identities and Sequences all well explained. But then UPDATE based on a JOIN, DELETE with TOP and OFFSET-FETCH and off course the OUTPUT clause with all its possibilities. I think a lot of production code will be optimized after reading this chapter.

**Chapter 9: Transactions and Concurrency**
  
The chapter starts with the theory of transactions, locks, blocks and some copy and paste code to start troubleshooting blocking issues on your production servers. But the decent explanation of how the Isolation Levels work with simple examples makes it understandable for everyone.

**Chapter 10: Programmable Objects**
  
The last chapter in the book is about variables, batches, cursors, procedures,etc. … And because they are not considered as fundamentals you get an overview with decent examples to get you started. 

**Conclusion**
  
If you need to write queries with T-SQL, this is a book that will get you started. After every chapter you'll find also a number of exercises to test what you have learned.

For me the book is decent value for money, now let's see when I can schedule my exam.

 [1]: http://www.microsoft.com/learning/en/us/exam.aspx?ID=70-457
 [2]: http://shop.oreilly.com/product/0790145321978.do
 [3]: http://tsql.solidq.com/
 [4]: https://twitter.com/ItzikBenGan