---
title: MySQL MyISAM and InnoDB Engine Differences
author: Jes Borland
type: post
date: 2012-01-10T14:48:00+00:00
ID: 1485
excerpt: 'I am a stranger in a strange land. I am a SQL Server DBA and developer wandering, lost, in the world of MySQL. Fundamentally, I know that a database is a database. Both MySQL and SQL Server are built on the same ANSI standards. However, as I started wor&hellip;'
url: /index.php/datamgmt/dbprogramming/mysql-myisam-and-innodb-engine/
views:
  - 5839
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - MySQL
  - MySQL Admin

---
I am a stranger in a strange land. I am a SQL Server DBA and developer wandering, lost, in the world of MySQL. Fundamentally, I know that a database is a database. Both MySQL and SQL Server are built on the same ANSI standards. However, as I started working with the MySQL databases of a couple websites, I ran into unexpected limitations in MySQL. 

SQL Server has one database engine. 

MySQL has, as of version 5.6, ten. Ten database engines. Each with different features. In-depth information about each can be found at <http://dev.mysql.com/doc/refman/5.6/en/storage-engines.html>. 

The one primarily being used in the databases I've been working with is [MyISAM][1]. It's a fast engine, designed for databases with a lot of read activity. The drawback to this, for me, is that it lacks both support for foreign keys and transactions. 

![][2]

I discovered this when I was looking in a database, at a table of resource articles. There was also a table of resource categories. I was trying to find the foreign key between the two tables. I even exported the structure out of phpMyAdmin, imported into [Toad for MySQL][3], and created an ERD. There were no foreign keys in the database. After doing some research, I found out that MyISAM simply does not support foreign key constraints. 

While writing some code for this database, I came to another realization: the MyISAM engine doesn't support transactions. I can't wrap a SQL statement in "BEGIN TRAN...COMMIT TRAN". You can lock tables, however. 

As of MySQL 5.5, the default engine type became [InnoDB][4]. This engine supports both foreign keys in tables and transactions. These tables also require more space on disk, and updates require more memory. So, there are trade-offs. 

One other thing to note is that within a single database, tables can be of different engine types. If it makes sense for some of your tables to be formatted as MyISAM for speed and others to be InnoDB for transactional consistency, you can design the database this way. This flexibility is a really nice option. 

Another lesson learned!

 [1]: http://dev.mysql.com/doc/refman/5.6/en/myisam-storage-engine.html
 [2]: http://upload.wikimedia.org/wikipedia/en/thumb/f/f4/The_Scream.jpg/220px-The_Scream.jpg ""
 [3]: http://www.quest.com/toad-for-mysql/
 [4]: http://dev.mysql.com/doc/refman/5.6/en/innodb-storage-engine.html