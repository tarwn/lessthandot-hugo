---
title: 'SQL Server Management: Compatibility Levels'
author: Kevin Conan
type: post
date: 2013-03-28T11:12:00+00:00
ID: 2054
excerpt: 'So you think compatibility levels will solve all your problems?  Think again!'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sql-server-management-compatibility-levels/
views:
  - 19791
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - compatibility level
  - dba
  - sql server

---
There was a conversation on twitter today about SQL Server Compatibility Levels because someone's vendor wanted them to use level 80 (SQL 2000) on a SQL 2012 Instance. 

There are two issues with what that vendor wanted. The first is that SQL Server only supports Current Version + 2. Meaning SQL Server 2012 supports SQL Server 2005 and SQL Server 2008 (and 2008R2). SQL Server 2008 and 2008R2 have the same major release number (10). So what this vendor was asking for is not possible because compatibility levels in SQL Server 2012 only go back to 90 (SQL 2005).

The second (and in my opinion the bigger) issue is why does the vendor want the compatibility level set back at all? 

I've seen many people who thing that if you set the compatibility level back to a previous version, then everything that worked in that previous version will work now. For example, they had a SQL 2005 Instance which they migrated to SQL 2012 and they think that setting the compatibility level at 90 means all the discontinued commands will go back to working.

This is completely incorrect and a dangerous trap! Compatibility levels are meant to partially run the database in the version you selected while you troubleshoot, fix and then change the compatibility level to the current version. 

Doing a search on the internet, I cannot find anything directly from Microsoft about what the compatibility levels really do. However, I've seen and experienced firsthand that they do not allow discontinued code to suddenly work.

For example, in SQL 2005 you can use a command called "RAISEERROR" (note the use of two e's in the middle). If you try that command on a database in SQL 2012 (no matter what compatibility level you have it set at), it will not work because it was discontinued.

What I'm trying to say is this, you cannot skip testing and removing discontinued code by using compatibility levels. You must test, removed discontinued code and work on migrate deprecated code to a supported solution. It like the old saying "If something appears too good to be true, it probably is too good to be true".