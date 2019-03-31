---
title: 'Book Review: SQL Server Hardware'
author: Jes Borland
type: post
date: 2012-10-22T17:45:00+00:00
excerpt: My latest geek read was SQL Server Hardware by Glenn Berry.
url: /index.php/datamgmt/dbadmin/book-review-sql-server-hardware/
views:
  - 12786
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
It’s bookworm time again! Since I’m back in the throes of half marathon training, there’s been less time for me to grab a cup of coffee and read a book each morning, but I’m making time for it when I can. My latest geek read was [_SQL Server Hardware_][1] by Glenn Berry ([blog][2] | [twitter][3]).

<p style="text-align: center;">
  <a href="http://www.simple-talk.com/books/sql-books/sql-server-hardware/"><img src="/wp-content/uploads/users/grrlgeek/1199-SQLServerHardware_Cover_200h.gif?mtime=1350934731" alt="" /></a>
</p>

[][1]

This book is designed to help anyone who needs to build or troubleshoot a SQL Server system understand different processors and storage available, how to benchmark the hardware, and the differences in versions and editions of SQL Server.

I’ve never been a hardware nerd. I used to build my own desktop systems, but I found the process of comparing processor L2 and L3 cache sizes unbearably boring, and wondered if different types of memory were really going to make a difference when I went to surf the internet and write .NET programs. I am not the type to sweat over what hard drive speed is best (SSD, baby). I’d rather have someone hand me a box and tell me it’s the best there is.

However, there are times a DBA will need to provide input on what hardware to buy for a new system, and many times she will need to know what hardware is underlying a system to troubleshoot it. This book is a great primer for anyone who has been avoiding getting to know the base layer of their systems.

**Chapter 1: Processors and Associated Hardware** 

The motherboard and pieces that get attached to it are the core of your system. Glenn guides you through cache size, clock speed, hyper-threading, and different models to help make the best decision for your system. One thing to note here is that this book was written pre-SQL Server 2012. The licensing model for SQL Server has changed, and that may affect the type of system you buy now. He also covers memory types, NICs, and motherboards. Remember that everything has to work together, and you’ll be fine.

**Chapter 2: The Storage Subsystem** 

I never truly appreciated properly sized, properly configured storage until I became a consultant. There are so many options for storage out there – magnetic hard drives vs. SSD, DAS vs SAN, RAID 0 vs RAID 1 – that picking what to use can be overwhelming. Glenn breaks the options down in an easy-to –understand format.

**Chapter 3: Benchmarking Tools** 

Benchmarks are a useful way for you to understand what your hardware is capable of before you ever install a piece of software on the server. Glenn talks about common application and component benchmarks. Application benchmarks, such as TPC-C, TPC-E, and TPC-H are used to predict the performance of hardware using a general database design and load. Glenn discusses the differences between them, and how to analyze the results. He also gives useful tools for component benchmarking, like Geekbench and CrystalDiskMark. The extra time taken to benchmark your hardware before using it can save you a lot of time in the long run.

**Chapter 4: Hardware Discovery** 

If you need to find out what hardware is in an existing system, there are several tools available. Glenn talks about common tools I use frequently, such as CPU-Z and even Task Manager. Knowing what hardware I have to work with makes a difference in how I troubleshoot problems!

**Chapter 5: Operating System Selection and Configuration** 

I always want to pick the latest and greatest OS, but that’s not always a possibility. It may not be supported by a piece of software that needs to run on that server. Here, Glenn discusses the difference between 32-bit and 64-bit. He breaks down Windows Server 2000, 2003, 2008, and 2008R2. What I really liked here is the table showing when Microsoft will stop offering both mainstream and extended support for various versions. It’s important to understand what support levels you have, and what happens when that goes away.

Glenn also talks about tweaks that can be made to the OS to make SQL Server perform at its best. These are great tips! I have a well-highlighted section that shows the increase in power usage and increase in performance between different power settings.

**Chapter 6: SQL Server Version and Edition Selection** 

Again, it would be nice to always pick the latest and greatest version of SQL Server, but that is not always practical or possible. Glenn focuses on SQL Server 2008 and 200R2 editions and features in this chapter. Knowing what changed between versions is always helpful. Knowing what features you can get when you move from Standard to Enterprise edition is also a big decision (data compression, anyone?). Sometimes, one feature can make the difference for your company. Keep in mind that SQL Server 2012 isn’t covered here, but also has a lot of great new features.

**Chapter 7: SQL Server Installation and Configuration** 

When installing SQL Server, it’s possible to kick off the wizard and click Next on every screen. Is that advisable though? No. Glenn covers what to do before you begin installation, such as updating all drivers. He talks about knowing what accounts you will use for the services and knowing where the data and log files are to be placed. He walks through the installation wizard. He also covers slipstreaming – the process of creating a package to install SQL Server and service packs or cumulative updates together. I love this ability and if you aren’t doing it, urge you to give it a try!

**Thank you, Glenn!** 

Overall, this is a great, information-filled book. It’s a reference I’ll keep on my bookshelf and use regularly. I’d like to thank Glenn for sharing his passion and knowledge with those of us that aren’t as in love with the nuances of hardware!

 [1]: http://www.simple-talk.com/books/sql-books/sql-server-hardware/
 [2]: http://www.sqlskills.com/blogs/glenn/
 [3]: http://twitter.com/glennalanberry