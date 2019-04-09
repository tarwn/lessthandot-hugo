---
title: 'Book Review: Troubleshooting SQL Server – A Guide for the Accidental DBA'
author: Jes Borland
type: post
date: 2012-08-01T11:58:00+00:00
ID: 1685
excerpt: My latest SQL Server read was Troubleshooting SQL Server – A Guide for the Accidental DBA, by Jonathan Kehayias and Ted Krueger.
url: /index.php/datamgmt/dbadmin/book-review-troubleshooting-sql-server/
views:
  - 12738
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
My latest SQL Server read was [Troubleshooting SQL Server – A Guide for the Accidental DBA][1], by Jonathan Kehayias ([blog][2] | [twitter][3]) and Ted Krueger ([blog][4] | [twitter][5]). This book is designed to teach people not familiar with the intricacies of SQL Server day-to-day troubleshooting steps and techniques.

 

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/TroubleshootingSQL.JPG?mtime=1343829419" alt="" />
</p>

Topics that are covered include disk I/O configuration, CPU, memory, indexes, blocking, deadlocks, transaction logs, and &#8211; my favorite &#8211; accidents waiting to happen.

**The Good** 

This isn't an in-depth look into how SQL Server works. (If that is what you’re looking for, I recommend [SQL Server 2008 Internals][6].) It's a high-level overview of what you need to know right now to keep SQL Server instances and databases running. It's well written and to the point. Each chapter includes a list of additional resources, which is invaluable.

After reading it, I can honestly say I wish I’d had it five years ago, when the DBA left my company and the reins were handed to me. I was a decent report writer, but didn’t know nearly enough about SQL Server administration to handle some of the problems I faced. I’m going to recommend this book to anyone just getting started as a DBA, or to anyone who has to deal with SQL Server part time. It’s a great reference.

**The Bad** 

With a few years of experience with SQL Server, I wanted a little more information in some sections. I reminded myself that wasn’t the goal of the book. There were a couple areas I thought could have used more basic information, such as no example of STOPAT in a RESTORE statement. Overall, though, it was a solid book that covered the basics.

This book is administration-focused. It does not cover any T-SQL or programming concepts. (I wasn’t expecting this, but if that’s what you’re looking for, this isn’t the right book either.)

**Chapter 1 – Troubleshooting** 

The troubleshooting method laid out begins with wait stats, and I agree that's a good place to start for most issues. What is mentioned at the end of the book, but not in this section, is that when things go wrong, the first thing to do is remain calm.

Under Plan Cache Usage, Jonathan states, “In my experience, the Plan Cache in SQL Server 2005 and 2008 is one of the most underused assets for troubleshooting performance problems in SQL Server.” This is true. You can view the most CPU- and disk-intensive queries, historically.

**Chapter 2 – Disk I/O Configuration** 

First, a thorough, easy-to-understand overview of RAID levels is presented. This is something very few people understand, but something that everyone in IT should know the basics of. The differences between Direct Attached Storage (DAS) and Storage Area Networks (SAN) are discussed. Knowing the fundamental differences between these two can affect a lot of decisions and troubleshooting. This chapter sets the stage for the rest of the book.

**Chapter 3 – High CPU Utilization** 

This chapter describes what happens when you have CPU pressure in your system. Perfmon, SQL Trace, and DMVs are used as troubleshooting tools. There are great sample queries to have in your DBA tool belt, such as “finding the top 10 CPU-consuming queries”. Problems that can cause high CPU usage, such as missing indexes and parameter sniffing, are a good take-away. How to deal with them is even better.

“Inappropriate parallelism” made me giggle.

**Chapter 4 – Memory Management** 

The memory section starts off talking about 32-bit architecture. I am really glad I don't have to support a lot of those systems, because I just don't get it. This was a perfect reference for me last week, too, as I worked with a client that was running a 32-bit SQL Server with 4 GB of RAM and was experiencing memory pressure.

How SQL Server, the OS, and memory interact is complicated to understand. One of the biggest things I've learned is how to monitor memory usage, which is covered nicely here. That is more important than anything else related to memory management.

**Chapter 5 – Missing Indexes** 

There is a good review of index basics, stressing the difference between key columns and nonkey columns, the order of key columns, and included columns. The two main troubleshooting tools discussed are the Database Tuning Advisor and the missing index DMVs. I found it most interesting to compare how DTA and the DMVs work &#8211; they are not equal! The queries provided in this chapter are very useful for everyday troubleshooting.

**Chapter 6 &#8211; Blocking** 

When first starting as a DBA, it was hard to understand the difference between latches and locks, necessary to the operation of SQL Server, and blocking, which happens but isn’t desirable. This chapter covers the basics of the types of locks and latches, which is all an accidental DBA really needs to understand at first. Queries using DMVs are given to help troubleshoot what tasks are blocked or waiting. The part I was most interested in was automatic detection and notification. In particular, I've started learning more about the blocked process report recently, and was happy to see information about it. Other topics like event notifications using Service Broker and Extended Events in SQL Server 2012 are covered.

**Chapter 7 – Handling Deadlocks** 

Several methods to gather deadlock information by capturing deadlock graphs are covered. Information on how to read the graphs is also included. It’s not enough to know what statements are causing the deadlock; how to fix it – or how not to fix it – is equally important. I agree with the advice here that issuing a KILL command isn’t always the best choice, and oftentimes the application code that caused the problem needs to be revisited.

**Chapter 8 – Large or Full Transaction Log** 

I have run into problems with transaction logs that filled up drives, or long-running transactions that took up a lot of space in the log, many times. The chapter starts with a quick review of how the transaction log works. Several reasons the log could grow outrageously are discussed. Then, methods to fix it such as taking a log backup or adding space are discussed. What’s equally important is the “what not to do” section. There is a lot of bad advice given on the internet, and I’m glad that issue is addressed here.

**Chapter 9 – Truncated Tables, Dropped Objects, and Other Accidents Waiting to Happen** 

The chapter starts with real-world stories of tables that have been dropped from production systems, whether through bad security practices or an accident. I’ve had moments like that, too – I’ve dropped tables from databases because of a missing WHERE clause. In real life, it happens. It’s how you react to it that’s important. Good guidelines are given, and the advice to “keep calm” is really important.

**A Practical Everyday Reference** 

This book is a great reference that is going to get dog-eared and highlighted very quickly. SQL Server is a complicated system that involves a lot of hardware and software pieces. Knowing the most common problems, and how to troubleshoot them, is a skill that should be learned by everyone using SQL Server. If you need a quick reference for the basics, pick up this book! Thank you to Jonathan and Ted for sharing their knowledge with us!

 [1]: http://www.simple-talk.com/books/sql-books/troubleshooting-sql-server-a-guide-for-the-accidental-dba/
 [2]: http://sqlskills.com/blogs/jonathan/
 [3]: https://twitter.com/SQLPoolBoy
 [4]: /index.php/All/?disp=authdir&author=68
 [5]: https://twitter.com/onpnt
 [6]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/book-review-microsoft-sql-server