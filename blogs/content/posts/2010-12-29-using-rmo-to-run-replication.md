---
title: Using RMO to run replication tasks
author: Ted Krueger (onpnt)
type: post
date: 2010-12-30T00:14:00+00:00
excerpt: 'RMO is not a new assembly and has been around a long time for use with replication.  In most cases RMO is extremely beneficial to merge replication when the subscriptions are editions like SQL Express.  This is due to the lack of a SQL Server Agent on the Express side.  With RMO you can run almost every task that involves replication including monitoring.'
url: /index.php/datamgmt/dbprogramming/using-rmo-to-run-replication/
views:
  - 5954
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - replication
  - rmo
  - sql server 2008

---
**</p> 

RMO and working with SQL Server Replication

</strong>

I’ve started to add [Wiki entries here on LessThanDot][1] for Replication Management Objects (RMO).  RMO is not a new assembly and has been around a long time for use with replication.  In most cases RMO is extremely beneficial to merge replication when the subscriptions are editions like SQL Express.  This is due to the lack of a SQL Server Agent on the Express side.  With RMO you can run almost every task that involves replication including monitoring.

I will build on the RMO listing in the Wiki area and keep this post updated for quick links to them.

As with anything published on LTD, if you know of more efficient methods or better coding practices please post your comments or start threads in the forums regarding them.  This keep the documentation we build on LTD of the highest quality for all of us to learn from.    

  * [RMO Enum all snapshot agents][2]
  * [RMO Enum all subscriptions][3]
  * [RMO Generate snapshots][4]
  * [RMO Get Array of publication Thresholds][5]
  * [RMO Load Publisher Properties][6]
  * [RMO Synchronize subscription][7]

 [1]: http://wiki.ltd.local/index.php/Category:Microsoft_SQL_Server
 [2]: http://wiki.ltd.local/index.php/RMO_Enum_all_snapshot_agents "RMO Enum all snapshot agents"
 [3]: http://wiki.ltd.local/index.php/RMO_Enum_all_subscriptions "RMO Enum all subscriptions"
 [4]: http://wiki.ltd.local/index.php/RMO_Generate_snapshots "RMO Generate snapshots"
 [5]: http://wiki.ltd.local/index.php/RMO_Get_Array_of_publication_Thresholds "RMO Get Array of publication Thresholds"
 [6]: http://wiki.ltd.local/index.php/RMO_Load_Publisher_Properties "RMO Load Publisher Properties"
 [7]: http://wiki.ltd.local/index.php/RMO_Synchronize_subscription "RMO Synchronize subscription"