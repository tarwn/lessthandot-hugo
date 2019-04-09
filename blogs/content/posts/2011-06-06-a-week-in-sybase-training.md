---
title: A week in Sybase Training, what did I learn, day 1
author: SQLDenis
type: post
date: 2011-06-06T18:12:00+00:00
ID: 1207
excerpt: |
  This whole week I am in Sybase training in NYC. I will write down short posts of mostly differences between Sybase and SQL Server each day and also what I have learned.
  
  First here is the awesome view from the Sybase office in the Grace building in Ne&hellip;
url: /index.php/datamgmt/datadesign/a-week-in-sybase-training/
views:
  - 22535
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - ase
  - sql server
  - sybase
  - training

---
This whole week I am in Sybase training in NYC. I will write down short posts of mostly differences between Sybase and SQL Server each day and also what I have learned.

First here is the awesome view from the Sybase office in the Grace building in New York City on the 32 floor

[<img src="http://farm4.static.flickr.com/3521/5804523937_c7f7c53eab.jpg" width="375" height="500" alt="Empire State Building View from the 32nd floor" />][1]

Here are the materials for this week of training.

[<img src="http://farm4.static.flickr.com/3590/5804584563_9acb76a291.jpg" width="500" height="375" alt="Sybase Training Materials" />][2]
  
  
That is a lot of stuff, by the end of the day I am sure my brain will be mush 🙁

One of the biggest differences between SQL Server and Sybase is of course that Sybase runs on Linus, Unix, Mac and Windows, SQL Server only runs on Windows.

#### System databases

SQL Server has the following system databases
  
master
  
model
  
tempdb
  
msdb
  
resource(a hidden db,files are mssqlsystemresource.mdf and mssqlsystemresource.ldf))

Sybase has the following system databases
  
master
  
model
  
tempdb
  
sybsystemdb(stores information about distributed transactions)
  
sybsystemprocs(Sybase system procedures are stored in the database sybsystemproc)

Certain system databases go on certain database devices, depending on the version these devices change, these devices are sysprocsdev, systemdbdev , master and tempdev

#### Installation

Interestingly enough, Sybase still installs with the sa account with a blank password. After the installation is done you need to change the password(Yeah you better or something like SQL Slammer for Sybase might bite you later on)
  
I learned about the various files and directories that are needed for a Sybase installation.
  
When installing Sybase, you install the following things with a typical install
  
Adaptive Server (this is the data engine)
  
Backup Server
  
Monitor Server
  
XP Server

A custom install would also give you the option of installing the Job Scheduler, the Job Scheduler is probably something like SQL Agent. The instructor said it is heavy weight, it uses a lot of process power.

**Interface file**
  
Sybase uses something called an interface file, this file lists the server and port of every known server
  
The file looks like this

<pre>ServerName
        master  tcp ether ServerName 5001
        query  tcp ether ServerName 5001</pre>

The interface file lives in the $SYBASE directory

**Page Size**
  
SQL Server only has 8K files, Sybase has 2K, 4K, 8K, 16k files.
  
This sounds great, however the page size is per server and if on one server the page size is 2K and the other server has 4K then you can't backup and restore the database on the other server, you need to script out the DB and then bcp all the data over, there was also another way mentioned by manipulating files.

Sybase also has no mixed extents, an 8K master database will be 4 times bigger than a 2K master database.

#### Memory

**ASE.exe
  
Server + Kernel**
  
This is something like number of open databases,open objects, open indexes, open partitions, number of locks
  
**proc cache
  
data cache**

This is done with the sp_config stored procedure, all these configurations are written in the ServerName.cfg file (in theory you can change it right there but if you add a bad value you might mess up your server and it won't start). 

The sp\_monitorconfig will show you the usage statistics. The procedure sp\_monitorconfig displays cache usage statistics regarding metadata descriptors for indexes, objects, and databases. sp_monitorconfig also reports statistics on auxiliary scan descriptors used for referential integrity queries, and usage statistics for transaction descriptors and DTX participants.

 [1]: http://www.flickr.com/photos/denisgobo/5804523937/ "Empire State Building View from the 32nd floor by Denis Gobo, on Flickr"
 [2]: http://www.flickr.com/photos/denisgobo/5804584563/ "Sybase Training Materials by Denis Gobo, on Flickr"