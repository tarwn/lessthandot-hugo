---
title: Database space use
author: David Forck (thirster42)
type: post
date: 2011-10-24T18:46:00+00:00
ID: 1353
excerpt: "I was doing some research on my SQL Servers and wanted to know just how much disk space an instance's database were taking up.  Since the databases on this instance are across a couple of different arrays and backups and other stuff exist on the server,&hellip;"
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/database-space-use/
views:
  - 4710
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I was doing some research on my SQL Servers and wanted to know just how much disk space an instance&#8217;s database were taking up. Since the databases on this instance are across a couple of different arrays and backups and other stuff exist on the server, I figured i&#8217;d write some t-sql to figure out this question. Here&#8217;s what I wrote:

sql
SELECT SUM(SIZE*8)/1024.0/1024.0 
FROM master.sys.sysaltfiles
```
That select statement shows me exactly how much space an entire instance (2005+) is taking up (save for any database in any state besides just online). This isn&#8217;t just the size of the data, but rather the space that the database files have taken up.

One particular use I have for this is determining how much junk is on my server taking up space and gives me a reason to grumble at people.

So, what&#8217;s your biggest instance? Mine&#8217;s currently 268gb.