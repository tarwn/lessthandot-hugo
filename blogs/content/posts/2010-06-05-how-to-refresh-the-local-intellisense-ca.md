---
title: How to refresh the local IntelliSense cache in SQL Server Management Studio
author: SQLDenis
type: post
date: 2010-06-05T12:12:00+00:00
ID: 810
excerpt: |
  This question came up again yesterday in our SQL Server forum so I decided to create a short blog post about it.
  The version of SQL Server Management Studio that ships with SQL Server2008 comes with IntelliSense enabled, I still think IntelliSense is s&hellip;
url: /index.php/datamgmt/dbprogramming/how-to-refresh-the-local-intellisense-ca/
views:
  - 21343
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
tags:
  - intellisense
  - tip
  - trick

---
This question came up again yesterday in our [SQL Server forum][1] so I decided to create a short blog post about it.
  
The version of SQL Server Management Studio that ships with SQL Server2008 comes with IntelliSense enabled, I still think IntelliSense is sometimes more in my way than it is useful but I won&#8217;t bore you with that. 

What will eventually happen is that if you create new tables and stored procedures IntelliSense will not know about those, when this happens you have to refresh the local IntelliSense cache for it to _see_ the new objects.

There are two ways to do it, if you are a shortcut impaired developer then here is how you do it. Click on Edit&#8211;>IntelliSense&#8211;>Refresh Local Cache

<img src="/wp-content/uploads/blogs/DataMgmt//Intelli2.PNG" alt=" refresh the local IntelliSense cache in SQL Server Management Studio" title=" refresh the local IntelliSense cache in SQL Server Management Studio" width="483" height="463" />

Or you can just hit the **CTRL + SHIFT + R** shortcut. Of course Query Analyzer users will remember CTRL + SHIFT + R being the shortcut to uncomment something.

Hopefully this will help someone 🙂

I also created a copy of this on our wiki here: [IntelliSense does not see new objects in SQL Server Management Studio][2]

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewtopic.php?f=17&t=11028
 [2]: http://wiki.ltd.local/index.php/IntelliSense_does_not_see_new_objects_in_SQL_Server_Management_Studio
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22