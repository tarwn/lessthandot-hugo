---
title: SQLCop update Version 1.1
author: George Mastros (gmmastros)
type: post
date: 2010-08-23T12:51:00+00:00
ID: 877
url: /index.php/datamgmt/dbprogramming/sqlcop-update-version-1-1/
views:
  - 7080
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
This version of [SQLCop][1] contains several bug fixes as well as some new features. Many of the items changed in this version were based on feedback received by you, our users. We encourage people to post comments so that we can improve the application.

If you previously downloaded the original version of SQLCop, you should manually download and install this new version: http://sqlcop.ltd.local/

**Fixes:**
  
1. This version of SQL Cop works with databases that have a binary collation.

2. There was a &#8220;load nodes&#8221; problem that occurred when your firewall was blocking your connection to our site. This version of the application does require an internet connection the first time it is run so that an XML file can be downloaded. After this first download, you no longer need an internet connection. Without a connection to the internet, you will not see the blog or wiki article that explains the problems, but you will still be able to list the occurrences of the problem in your database.

3. A database connection parameter was changed which will allow SQL Cop to run quicker.

4. Some people were seeing &#8220;stub&#8221; instead of &#8220;No Problems Found&#8221;. This item has been corrected.

**Updates**
  
SQL Cop now checks a very small XML file on startup that is located on our server. If the connection to the server fails, the local version of the issues XML file is used. If SQL Cop can download the configuration xml file, we compare versions of the application and versions of the issues xml. If there is a new application, the user is notified and prompted to download the newer version. If there is a newer version of the issues xml file, it is downloaded and stored automatically.

**Checks**
  
We added new checks for:
  
Code

  * Procedures with dynamic sql
  * Procedures using dynamic sql without sp_executesql

Table/View

  * Unnamed Constraints

Indexes

  * Forwarded Records

Configuration

  * Ad Hoc Distributed Queries
  * CLR
  * Database and log files on the same physical disk
  * Database Mail
  * Deprecated Features
  * Instant File Initialization
  * Max Degree of Parallelism
  * OLE Automation Procedures
  * Service Account
  * SMO and DMO
  * SQL Server Agent Service
  * xp cmdshell

A new category was added for Health with the following checks.

  * Buffer cache hit ratio
  * Page life expectancy

This brings the total number of issues checked to 50 for SQL 2008 and 49 for SQL 2005.

 [1]: http://sqlcop.ltd.local/