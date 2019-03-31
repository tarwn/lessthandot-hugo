---
title: An Interview With Kevin Kline About SQL In A Nutshell Third Edition
author: SQLDenis
type: post
date: 2009-02-04T12:42:52+00:00
url: /index.php/datamgmt/dbprogramming/an-interview-with-kevin-kline-about-sql/
views:
  - 14047
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - MySQL
  - Oracle
tags:
  - book
  - interview
  - mysql
  - oracle
  - postgresql
  - sql server 2008

---
Most of the database developers these days don&#8217;t work with just one RDBMS, a large percentage will work with at least two of them. [SQL In A Nutshell][1] will help you with that, no longer do you have the need for two or more books open at the same time. I asked Kevin Kline if he would be interested in an interview and as you can see he said yes.

**Denis: Is the book geared towards a beginner/intermediate level user or do you have to be an advanced user to really utilize the information in this book?**

KK> This book is more of an intermediate + book, since it is a syntax reference. For example, the book assumes you know what command you want to look up and doesn&#8217;t spend a lot of time educating you as to which command you should use for a given business requirement, as is the case with most O&#8217;Reilly books from the &#8220;In a Nutshell&#8221; series. For beginning SQL, I recommend &#8220;Learning SQL&#8221; also published by O&#8217;Reilly & Associates.

**Denis: What are the most important things a person can do to master SQL?**

KK> Practice! You simply cannot expect to master SQL, or any programming language for that matter, without doing a lot of it.

**Denis: What part of SQL do people struggle the most with?**

KK> There are a couple areas that tend to be programmatic for those who are new to SQL. But I&#8217;d say that the biggest area of struggle, far and away, is getting used to Set Theory. Set theory governs how data is managed in large sets of records. It&#8217;s the way that relational databases manipulate data, but most people especially those who&#8217;ve programmed in other popular languages are used to thinking about data one row at a time. This lead to performance problems.

**Denis: Between the second edition and the third edition of your book, from a pure SQL standpoint which RDBMS has added the most features?**

KK> If I&#8217;m measuring the most progress against the ANSI standard, I&#8217;d have to say that MySQL has added the most new SQL features. On the other hand, MySQL started from a deficit, meaning that in early editions of my book, MySQL didn&#8217;t meet the ANSI standard for many commands and functions. So just by complying with the ANSI standard in their latest release, they&#8217;ve added the most new features. If I&#8217;m measuring by the most new features regardless of the ANSI standard, I&#8217;d have to say that Microsoft SQL Server has made the most progress with their many new data types and commands. By comparison, Oracle tends to add new features via add-on products outside of the main relational engine which you must buy separately, while SQL Server adds them at no added charge right in the box. An example of this might include the spatial data type, in the box with SQL Server, but a separate bolt-on with Oracle.

**Denis: From each RDBMS covered in your book can you name your favorite feature which is unique to that RDBMS?**

KK> I cover fewer database in the 3rd edition than in the 2nd edition since few readers expressed an interest in continuing coverage of IBM&#8217;s DB2 UDB or Sybase&#8217;s Adaptive Server. Having said that, we still cover the ANSI standard plus the four most popular database &#8211; Oracle, Microsoft SQL Server, MySQL, and PostgreSQL. For Oracle, I love the incredibly powerful MODEL statement and the DECODE function. They rock! For SQL Server, I enjoy the PIVOT and UNPIVOT statements and its XML handling features. MySQL has a very nifty capability of utilizing several different transaction processing engines, such as InnoDB and MyISAM, some of which focus on raw processing power by dispensing with the niceties. And I really, really like PostgreSQL unwaivering support of the ANSI standard in all of their SQL statements.

**Denis: Should people write pure ANSI SQL or are there times when it is okay to write proprietary SQL?**

KK> I&#8217;m a big believer in going for performance, and that means writing proprietary code. Most internal IT shops should usually go with the proprietary code since the application will be for an internal customer who will run the application on only one database platform. On the other hand, most ISV&#8217;s write their database applications for multiple database platforms to support the widest possible customer base and, invariably, these applications are always performance wastelands. ANSI SQL is the place to get your fundamental schooling from, but in my opinion, it should not be the destination to take your application to.

**Denis: Is there a large percentage of people who work with more than one RDBMS at the same time?**

KK> We&#8217;d seen a long time span from the late 1990&#8217;s til late in this decade where most database professionals focused on only one database. However, something that has happened in the last two years is a major upsurge in corporate mergers and acquisitions. As a result, many more people are being asked to take on a database platform outside of their comfort zone. I&#8217;d say that the percentages have shifted from about 15% of multi-platform enterprise DBAs a few years ago to as much as 25% today.

**Denis: What SQL books are on your bookshelf?**

KK> I still keep a copy of SQL99: Complete &#8211; Really! as well as the ANSI 2006 SQL standard. I have Celco&#8217;s SQL for Smarties, Feuerstein&#8217;s PL/SQL Programming, and Ben-Gan&#8217;s Inside Transact-SQL Programming and Inside Transact-SQL Queries. In addition, I&#8217;ll always keep a copy of my late friend Ken Henderson&#8217;s The Guru&#8217;s Guide to Transact-SQL for sentimental reasons. I also have general database books about all the database platforms.

**Denis: Why do you write technical books?**

KK> It started out with a bit of an ironic twist. When I wrote my first book in the early 1990&#8217;s, I was planning on completing a master&#8217;s degree with a thesis. I pitched the thesis abstract to a book publishing company and was surprised when the accepted it. And the simple truth is that I always wanted to write and enjoy writing. That&#8217;s why I still do it today.

**Denis: Which chapter was the hardest to write and can you explain why?**

KK> There&#8217;s no doubt that the section of SQL in a Nutshell that covers ALTER, CREATE, and DROP statements was the most difficult. And I fully blame Oracle for this. Here&#8217;s an example derived from the vendor documentation. The CREATE TABLE statement for MySQL and PostgreSQL runs to about 25 pages each. The same statement has more variations on Microsoft SQL Server and so runs to about 55 pages in their documentation. But on Oracle, the CREATE TABLE statement runs well over 160 pages with more variation, permutations, and sub-permutations than is imaginable. No offense to the vendors here, but there is such a thing as overloading a statement or function with too much weight. So you can imagine how much time and energy it took to distill these statements down to a digestable size!

**Denis: Who are your favorite authors?**

KK> In the database world, my two favorite authors are Kalen Delaney of Inside SQL Server fame and Steven Feuerstein, author of PL/SQL Programming and many other excellent Oracle books and a fine blog as well. Outside of the database world, I&#8217;m a voracious reader on all topics, both fiction and non-fiction. However, I will always read anything put out by two non-fiction writers &#8211; Jared Diamond, author of Guns, Germs and Steel, and Michael Pollan, author of The Omnivore&#8217;s Delimma.

**Denis: Where can we expect to see you in 2009? Any conferences, seminars, trade shows or classrooms perhaps?**

KK> It&#8217;s no secret that the economy has taken a downturn. So it&#8217;s almost a certainty that I&#8217;ll speak at fewer local user groups this year than in years past. However, I&#8217;m sure to be at TechEd this May and at the PASS 2009 Summit in Seattle this fall. I hope to see you there!

Thanks goes out to Kevin Kline for doing the interview, here is some info on the book and I also provided an Amazon link, make sure to check it out

[SQL in a Nutshell, Third Edition A Desktop Quick Reference Guide][1]
  
By Kevin Kline, Daniel Kline, Brand Hunt
  
Published: November 2008
  
Pages: 591
  
Series: In a Nutshell

Description
  
The essential reference to the SQL language used in today&#8217;s most popular database products, this new edition of SQL in a Nutshell clearly documents every SQL command according to the latest ANSI standard. It also details how these commands are implemented in the Microsoft SQL Server 2008 and Oracle 11g commercial database packages, and the MySQL 5.1 and PostgreSQL 8.3 open source database products.

If you are interested in Kevin Kline&#8217;s blog, you can find that here: http://sqlblog.com/blogs/kevin_kline/default.aspx

 [1]: http://www.amazon.com/gp/product/0596518846?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=0596518846