---
title: SQL Server DBA Tip 11 – SQL Server Configuration – MIN Memory
author: Ted Krueger (onpnt)
type: post
date: 2011-05-10T12:40:00+00:00
ID: 1166
excerpt: 'Tip 11 of this series is being added in as the topic has come up during the series on several occasions.  Specifically, the question was raised on the Twitter hash tag #SQLHELP of, “What are some good use-cases where setting min-server memory makes sens&hellip;'
url: /index.php/datamgmt/dbadmin/sql-server-dba-tip-sql-server-min-memory/
views:
  - 4001
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Tip 11 of this series is being added in as the topic has come up during the series on several occasions.  Specifically, the question was raised on the Twitter hash tag #SQLHELP of, “What are some good use-cases where setting min-server memory makes sense?”

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-55.png?mtime=1304943624"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-55.png?mtime=1304943624" width="93" height="92" align="left" /></a>
</div>

There really isn’t an answer to this question other than: the minimum memory value should always be set given the physical resources.  We’ve discussed the importance of setting the [MAX Memory value][1].  Today we are going to go over the MIN Memory value. 

**What does the MIN Memory setting do?**

The primary goal of the MIN memory value is to prevent windows from taking too much memory from SQL Server.  This would directly impact performance of SQL Server.  Allowing memory to be released from SQL Server to a point that SQL Server is starved has the same effect of SQL Server and the physical resources not being available to begin with.

For example: If you configure a physical Windows Server for 8GB of physical RAM and set a MAX Memory of 6GB but have a SQL Server instance that would benefit from 24GB of physical RAM, you would notice a severe performance situation.  This would be due to the lack of physical memory for the entire server to perform optimally.

**What should you set the MIN Memory value to?**

The MIN Memory setting on SQL Server should be set to a value that will allow SQL Server to perform optimally given the usage you collect from baseline and other resources (vendor documentation and/or monitoring).  One sound default that has been used in several variations of SQL Server configurations is half the distance to 0 from the MAX Memory setting.

So if MAX memory setting is 8GB, your MIN Memory setting would be set to 4GB.  This would always allocate the 4GB to SQL Server.

**Remarks on MIN/MAX Memory settings**

MIN Memory setting should never be the same as MAX Memory setting.  MIN Memory does not mean that SQL Server will immediately be allocated that amount of memory on startup.  SQL Server will start consuming memory as it needs memory.  If SQL Server has a MIN Memory setting of 4GB and MAX Memory setting of 8GB but only requires 1GB of memory if set idle, SQL Server will only consume ~1GB at the point it needs it.  As time goes on, SQL Server will require memory to operate internally so memory consumption will rise.  Monitoring that type of growth even if external requests are not occurring is a normal activity. 

Resources

[Optimizing Server Performance Using Memory Configuration Options][2]

 [1]: /index.php/DataMgmt/DBAdmin/sql-server-dba-tip-1
 [2]: http://msdn.microsoft.com/en-us/library/ms177455.aspx