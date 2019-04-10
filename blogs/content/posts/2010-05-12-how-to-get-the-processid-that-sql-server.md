---
title: How to get the processid that SQL Server is using if you have multiple instances of SQL Server running
author: SQLDenis
type: post
date: 2010-05-12T11:58:28+00:00
ID: 789
url: /index.php/datamgmt/dbprogramming/how-to-get-the-processid-that-sql-server/
views:
  - 18981
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - how to
  - sql server 2005
  - sql server 2008
  - tip

---
I have two instances of SQL Server running on my laptop, one is SQL Server 2008 and the other is SQL Server 2008 R2. Now take a look at this image from task manager.

<img src="/wp-content/uploads/blogs/DataMgmt//TaskManager.PNG" alt="" title="" width="737" height="236" />

Can you tell which process is the 2008 instance and which process is the 2008 R2 instance? I can't either. Now if you want to know what process id your instance is using you can do three things

### 1) Use T-SQL

Run the following query in a query window

sql
select SERVERPROPERTY('processid'),SERVERPROPERTY('productversion')
```

I get the following as output when running that query on both instances

<pre>2212	10.0.2531.0
2184	10.50.1600.1</pre>

So, now I know that the 2008 instance is using processid 2212 and the 2008 R2 instance is using 2184

### 2) Look at the SQL Server log

If I look at my SQL Server log, I see the following

05/12/2010 08:24:25,System Manufacturer: 'Hewlett-Packard'<c> System Model: 'HP Compaq 8510p'.
  
05/12/2010 08:24:25,Server **process ID is 2184**.
  
05/12/2010 08:24:25,All rights reserved.
  
05/12/2010 08:24:25,(c) Microsoft Corporation.
  
05/12/2010 08:24:25,Microsoft SQL Server 2008 R2 (RTM) – 10.50.1600.1 (X64) <nl> Apr 2 2010 15:48:46 <nl> Copyright (c) Microsoft Corporation<nl> Developer Edition (64-bit) on Windows NT 6.1 <x64> (Build 7600: )

### 3) Use the services tab in task manager

This is probably the easiest one, just switch to the services tab

<img src="/wp-content/uploads/blogs/DataMgmt//Services.PNG" alt="" title="" width="614" height="183" />

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins></x64></nl></nl></nl></c>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22