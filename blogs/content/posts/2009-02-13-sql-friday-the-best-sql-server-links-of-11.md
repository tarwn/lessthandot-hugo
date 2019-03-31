---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 11
author: SQLDenis
type: post
date: 2009-02-13T17:28:51+00:00
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-11/
views:
  - 5299
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Here we are with another mesmerizing episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show.
  
Here is what I found interesting this past week in SQL Land:

**[All indexes are unique][1]**
  
Gail Shaw tells us about the uniquefier which makes indexes unique

**[Performance impact: a large number of virtual log files – Part I][2]**
  
**[Performance impact: a large number of virtual log files – Part II][3]**
  
Linchi Shea&#8217;s two part post explores two questions
     
1. Is a large number of VLFs in a transaction log still a significant performance factor in SQL Server 2008?
     
2. Does a large number of VLFs have an adversely impact on application-related operations such as INSERT, UPDATE, and DELETE?

**[Released: SQL Server 2008 Extended Events Manager][4]**
  
Jonathan Kehayias writes: This started out as a pet project in August 2008 when I decided that if I was ever going to get good use out of Extended Events in SQL Server 2008, I needed to be able to see the layout of the metadata graphically. What started out as a simple TreeView that displayed the MetaData in a sensible (at least to me) manner slowly began to transform into a complete application that could build and manage Extended Event Sessions as well. 

**[Bit vs. Char(1) in SQL Server][5]**
  
Aaron Alton shows us some reasons why bits rock, and I agree with him

**[Estimating Rows per Page][6]**
  
Michelle Ufford shows us some code which we can use to estimate rows per page

**[How It Works: File Stream Compression with Backup/Restore][7]**
  
The PSS SQL Server Engineers show how File Stream Compression compression works with Backup/Restor



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][8] forum or our [Microsoft SQL Server Admin][9] forum**<ins></ins>

 [1]: http://sqlinthewild.co.za/index.php/2009/02/09/all-indexes-are-unique/
 [2]: http://sqlblog.com/blogs/linchi_shea/archive/2009/02/09/performance-impact-a-large-number-of-virtual-log-files-part-i.aspx
 [3]: http://sqlblog.com/blogs/linchi_shea/archive/2009/02/12/performance-impact-a-large-number-of-virtual-log-files-part-ii.aspx
 [4]: http://sqlblog.com/blogs/jonathan_kehayias/archive/2009/02/09/released-sql-server-2008-extended-events-manager-build-1-0-3-114-stable.aspx
 [5]: http://feedproxy.google.com/~r/TheHobt/~3/v-RnN0rVeJo/bit-vs-char1-in-sql-server.html
 [6]: http://feedproxy.google.com/~r/SqlFool/~3/4iOti0-0PaQ/
 [7]: http://blogs.msdn.com/psssql/archive/2009/02/11/how-it-works-file-stream-compression-with-backup-restore.aspx
 [8]: http://forum.ltd.local/viewforum.php?f=17
 [9]: http://forum.ltd.local/viewforum.php?f=22