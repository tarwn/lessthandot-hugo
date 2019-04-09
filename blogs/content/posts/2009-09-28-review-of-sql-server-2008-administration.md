---
title: Review of SQL Server 2008 Administration in Action
author: SQLDenis
type: post
date: 2009-09-28T17:47:40+00:00
ID: 570
url: /index.php/datamgmt/dbprogramming/review-of-sql-server-2008-administration/
views:
  - 17205
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - book
  - database
  - sql server 2008

---
I have finished reading [SQL Server 2008 Administration in Action][1] by Rod Colledge and below is my review of this book

I will start by saying that I truly enjoyed this book. The book is written in a casual way and is not filled with tons of code, concepts are explained well and where needed images are included to help you understand it better. I also like the fact that this is not a 1000 page monster which weighs more than my laptop; it is 464 pages and fits nicely in my bag.

This book is filled with best practices and common sense. Each chapter ends with best practice consideration. The 3 page best practices considering security section in chapter 6 is something every SQL Server developer, administrator and especially an accidental DBA should memorize.

The suggested work plan for a DBA is something I needed for a long time; it is there all in one place broken down by daily, weekly and monthly tasks. Besides best practices I found that this book also has the top 25 DBA worst practices…..I guarantee that almost everyone will have a couple of items on that list; now you have a chance to eliminate some of those.

Things that were introduced in SQL Server 2008 are marked with _New In 2008_, this enables someone with previous experience to quickly jump to those section and focus on the new stuff. Most of the material in this book also applies to previous versions of SQL Server; if you are not on version 2008 yet but will be in a year or so then this is good way to get yourself familiar with the new stuff while implementing best practices for your current environment.

SQL Server administration is not just about SQL Server; you also need to know how the windows operating system itself works, what the different types of RAID are, what kind of memory to get etc etc. reading SQL Server 2008 Administration in Action does a good job explaining all these things, there are links to resources where you can find more detail about a particular subject and there are also links to tools that might be helpful creating the optimal server for your SQL Server installation

This book is organized in 3 parts: Planning and installation, Configuration and Operations. Below is the breakdown of the chapters

Planning and installation
  
Chapter 1 The SQL Server landscape
  
Chapter 2 Storage system sizing
  
Chapter 3 Physical server design
  
Chapter 4 Installing and upgrading SQL Server 2008
  
Chapter 5 Failover clustering

Configuration
  
Chapter 6 Security
  
Chapter 7 Configuring SQL Server
  
Chapter 8 Policy-based management
  
Chapter 9 Data management

Operations
  
Chapter 10 Backup and recovery
  
Chapter 11 High availability with database mirroring
  
Chapter 12 DBCC validation
  
Chapter 13 Index design and maintenance
  
Chapter 14 Monitoring and automation
  
Chapter 15 Data Collector and MDW
  
Chapter 16 Resource Governor
  
Chapter 17 Waits and queues: a performance-tuning methodology

appendix A Top 25 DBA worst practices
  
appendix B Suggested DBA work plan
  
appendix C Common Performance Monitor counters
  
appendix D Top 10 Management Studio enhancements
  
appendix E Date/time data types in SQL Server 2008

I would recommend this book to anyone who has to manage SQL Server, it doesn’t matter if the machines are already up and running or if you still have to install everything. The people who will benefit the most from this book are the accidental DBAs; these are the people who never managed a server before but because the company decided to downsize they became the DBA and it is only a matter of time before something happens.

The material in this book will make your job easier; it will make you a better administrator and why not invest in yourself by getting this book?

Below are 2 chapters that you can download to check out the book

Sample chapter 4 [Installing and upgrading SQL Server 2008][2]
  
Sample chapter 10 [Backup and recovery][3]

There is some more info about the book on the publisher's website: http://www.manning.com/colledge/

You can also checkout the book on Amazon for more reviews: [SQL Server 2008 Administration in Action][1]



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][4] forum or our [Microsoft SQL Server Admin][5] forum**<ins></ins>

 [1]: http://www.amazon.com/gp/product/193398872X?ie=UTF8&tag=sql08-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=193398872X
 [2]: http://www.manning.com/colledge/SampleCh04.pdf
 [3]: http://www.manning.com/colledge/SampleCh10.pdf
 [4]: http://forum.ltd.local/viewforum.php?f=17
 [5]: http://forum.ltd.local/viewforum.php?f=22