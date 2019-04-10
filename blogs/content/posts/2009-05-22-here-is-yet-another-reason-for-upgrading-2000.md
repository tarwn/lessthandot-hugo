---
title: Here Is Yet Another Reason For Upgrading To SQL Server 2008 From SQL Server 2000
author: SQLDenis
type: post
date: 2009-05-22T13:08:08+00:00
ID: 442
url: /index.php/datamgmt/dbprogramming/here-is-yet-another-reason-for-upgrading-2000/
views:
  - 3990
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql server 2000
  - sql server 2008

---
I had to restore a bunch of databases yesterday from our live server running on SQL Server 2000 to a server running SQL Server 2008. These databases range in size from 5 GB to well over 100 GB. I remember when I scripted out the biggest database with all the filegroups and ran that script on a 2000 box it took over an hour to create this database. 

The reason for this is that all the filegroups get filled with zeroes. In SQL Server 2008 (and SQL Server 2005) this doesn't work like that anymore. When you create the database, the filegroups don't get filled with zeroes anymore. When I took the script that ran for over an hour and ran it on the SQL Server 2008 box it finished in under a minute.

I decided to take this for a test I ran the following script on both a 2000 and a 2008 box, both are desktop XP machines. The script will create a database named test with a datafile of 3012.00 MB and a log file of 2321.00 MB

sql
CREATE DATABASE [Test]  ON (NAME = N'Test_Data', 
FILENAME = N'C:Test.MDF' , SIZE = 3012, FILEGROWTH = 10%) 
LOG ON (NAME = N'Test_Log', FILENAME = N'C:Test_Log.LDF' , SIZE = 2321, FILEGROWTH = 10%)
 COLLATE SQL_Latin1_General_CP1_CI_AS
GO
```

The CREATE DATABASE process is allocating 3012.00 MB on disk 'Test_Data'.
  
The CREATE DATABASE process is allocating 2321.00 MB on disk 'Test_Log'.

The 2008 box was over 3 times as fast

SQL Server 2000: 3 minutes and 10 seconds
  
SQL Server 2008: 55 seconds

I decided to do another test and made the database bigger. This time I created a database named test with a datafile of 6012.00 MB and a log file of 4321.00 MB

First you need to drop the database we created before

sql
drop database test
go
```

sql
CREATE DATABASE [Test]  ON (NAME = N'Test_Data', 
FILENAME = N'C:Test.MDF' , SIZE = 6012, FILEGROWTH = 10%) 
LOG ON (NAME = N'Test_Log', FILENAME = N'C:Test_Log.LDF' , SIZE = 4321, FILEGROWTH = 10%)
 COLLATE SQL_Latin1_General_CP1_CI_AS
GO
```

The CREATE DATABASE process is allocating 6012.00 MB on disk 'Test_Data'.
  
The CREATE DATABASE process is allocating 4321.00 MB on disk 'Test_Log'.

The 2008 box was still over 3 times as fast

SQL Server 2000: 6 minutes and 14 seconds
  
SQL Server 2008: 1 minutes and 45 seconds

This is a nice little benefit when upgrading, on a real server you should even see a bigger difference.

If you want you can run these scripts on your machine and then leave me the results in a comment.