---
title: 'Book Review: Pro SQL Server 2008 Failover Clustering'
author: Jes Borland
type: post
date: 2013-06-25T12:48:00+00:00
ID: 2117
excerpt: This is the most comprehensive look at Windows and SQL Server failover clustering that exists. In this book, Allan Hirt, a clustering expert and Microsoft MVP, lays a solid foundation for all aspects of clustering.
url: /index.php/itprofessionals/book-review/book-review-pro-sql-server-2/
views:
  - 8184
rp4wp_auto_linked:
  - 1
categories:
  - Book Review

---
Despite having worked with SQL Server clusters for years, I didn't pick up the book [Pro SQL Server 2008 Failover Clustering][1] and read it cover-to-cover until recently. This is the most comprehensive look at Windows and SQL Server failover clustering that exists. In this book, Allan Hirt ([b][2] | [t][3]), a clustering expert and Microsoft MVP, lays a solid foundation for all aspects of clustering.

<p style="text-align: center;">
  <img src="http://ecx.images-amazon.com/images/I/51ET4iyVwSL.jpg" alt="" width="185" height="250" />
</p>

The book covers four main areas – the basics of failover clustering, clustering Windows Server 2008, clustering SQL Server 2008, and administration of a clustered SQL Server 2008 instance.

(Yes, this edition is focused on Windows Server 2008 and SQL Server 2008. [Allan is working on an updated version][4]!)

### High Availability 101

The basics of high availability are covered in the first chapter. Why do you need HA? What HA methods and technologies are available? Failover clustering, log shipping, database mirroring, and replication are discussed here, with great illustrations. It’s important to understand the differences between the methods. A comparison of these methods can help you determine, “Is this right for my company, my application, and my budget?”

### Clustering Windows Server

The next section is all about clustering Windows Server. If you’re a DBA thinking, “I don’t worry about that, I let my server administration team take care of it”, you have the wrong attitude. You will not succeed in today’s technological landscape with a narrow focus. You need to have at least a basic understanding of how all the parts of your applications, your servers, and your network work together. This is even more important because SQL Server failover clustering is entirely dependent on Windows Server clustering. If you don’t cluster Windows, you can’t cluster SQL Server.

Allan takes a comprehensive look at what is involved in server clustering. You need to choose supported hardware. You have to set up accounts. The networking components must be configured. The concept of quorum is explained – and Windows Server 2008 includes new models, so even if you’re familiar with disk or majority node set, there are new things to learn.

He then gives an overview of preparing Windows Server. There is a step-by-step walkthrough of installing and configuring the network, features and roles, disks, domain tasks, and configuration. My favorite part of this book is the in-depth walk-throughs. There are step-by-step screenshots from the GUI, along with command line or PowerShell scripts if applicable.

The next walk-through is on clustering Windows. Validation must be performed first, and then the cluster can be created. Important post-installation checks and verification are included. The steps here are very comprehensive.

### Clustering SQL Server

The next two chapters are dedicated to preparing to cluster SQL Server, then actually doing it. He reviews which components of SQL Server can be clustered, and which can’t. Planning needs to be thorough and well-thought-out, especially as you move beyond a two-node cluster. Which disks will host the quorum, data, and log files? Which instances will be installed on which nodes? How will you manage memory? After these things have been determined, then you can proceed with installing SQL Server, node by node.

There is a new method for SQL Server installation, cluster preparation, which is also covered. Each node can have some information configured on it, then, when you are ready, in a few short steps, you can finish installation on the nodes.

There is a section dedicated to upgrading to SQL Server 2008 failover clustering. As with any upgrade, there are business and technical challenges to be considered, including testing and a rollback plan. The steps to do an in-place upgrade are covered from start to finish.

### Administration and Virtualization

Administration is not overlooked. There are some tasks that are unique to failover clusters, such as adding or removing nodes, or putting disks in maintenance mode. You should read this chapter before clustering, so you know what you can do, and keep it as a reference for after your clusters are installed, in case a problem arises.

Last but not least, there’s a discussion about clusters and virtualization. Face it, virtualization is here to stay. This is another area where DBAs need to expand their skillset. Understanding how clusters will behave on virtual instances – the performance impact, the HA impact – is very important if you’re faced with this in your environment. The best part? Allan shows you how to set up a clustered instance using VMware Workstation – which can help you build a virtual lab to play with!

This book is a must-have for the DBA’s bookshelf. If you aren’t working with failover clusters now, there is a good chance you will at a future point in your career. Even though terms and options may change in new versions of Windows Server and SQL Server, you should work to understand the concepts now.

I can’t wait for the next version!

 [1]: http://www.amazon.com/Server-Failover-Clustering-Experts-Voice/dp/1430219661
 [2]: http://www.sqlha.com/blog/
 [3]: https://twitter.com/SQLHA
 [4]: http://www.sqlha.com/2013/06/03/announcement-day-windows-server-2012-r2-sql-server-2014-and-my-mission-critical-book/