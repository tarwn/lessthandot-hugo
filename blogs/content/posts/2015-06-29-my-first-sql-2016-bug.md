---
title: My first SQL 2016 bug
author: Koen Verbeeck
type: post
date: 2015-06-29T11:40:05+00:00
url: /index.php/datamgmt/dbprogramming/mssqlserver/my-first-sql-2016-bug/
views:
  - 23192
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - bug
  - sql 216
  - sql server

---
A little while back [SQL Server 2016 CTP2 was announced][1] and I have spent some time playing with it (read: spent hours creating a VM because there were hundreds of Windows updates).
  
It seems I have already found a very small bug while creating a new database:

[<img class="alignnone size-full wp-image-3430" src="http://blogs.ltd.local/wp-content/uploads/2015/06/firstBug.png" alt="firstBug" width="704" height="214" srcset="http://blogs.ltd.local/wp-content/uploads/2015/06/firstBug.png 704w, http://blogs.ltd.local/wp-content/uploads/2015/06/firstBug-300x91.png 300w" sizes="(max-width: 704px) 100vw, 704px" />][2]

Apparently they forgot to rename the compatibility level 🙂

 [1]: /index.php/datamgmt/dbprogramming/mssqlserver/sql-2016-preview-has-been-released/
 [2]: http://blogs.ltd.local/wp-content/uploads/2015/06/firstBug.png