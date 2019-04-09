---
title: SQL Server DBA Tip 4 – Server Considerations – 32 bit / 64 bit
author: Ted Krueger (onpnt)
type: post
date: 2011-04-29T10:11:00+00:00
ID: 1129
excerpt: |
  Having to make a decision between 32 bit and 64 bit servers is becoming less common.  64 bit servers are the predominant platform when purchasing hardware.  With that said, existing hardware still varies greatly between the two platforms. 
  Purchasing n&hellip;
url: /index.php/datamgmt/dbprogramming/sql-server-32bit-64bit/
views:
  - 5930
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server Admin

---
Having to make a decision between 32 bit and 64 bit servers is becoming less common.  64 bit servers are the predominant platform when purchasing hardware.  With that said, existing hardware still varies greatly between the two platforms. 

**Purchasing new hardware**

When purchasing new hardware, it will be difficult to find hardware that is only 32 bit.  Although 64 bit hardware is an accepted, better choice for scalability, primarily in regards to memory, 32 bit platforms are still wanted for certain situations.  Luckily, you can purchase 64 bit hardware and install a 32 bit operating system on that platform.  This is a major benefit in regards to applications that may, for some unknown reason, not support 64 bit operating systems.

The Operating System that is chosen is critical based on 32 bit and 64 bit as well.  With [Windows Server 2008 R2][1], 32 bit is no longer supported.  This forces any 32 bit installations to use an older version of Windows, such as Windows Server 2008.  That restriction can be enough to request a full 64 bit platform for new and upgraded installations. 

Features like [SQL Server Integration Services have limitations][2] in regards to 64 bit platforms.  That can break or make an upgrade to a 64 bit platform to increase throughput of memory utilization.  Also take into account that features such as SQL Server Reporting Services are based on a web server presence.  This type of dedicated server would be configured as such, more so than a full blown SQL Server Engine installation.

**AWE** 

Since the choice between 32 bit and 64 bit is much less of a problem, this tip includes the question, “Should AWE be enabled on 64 bit SQL Server installations?”

Enabling AWE on 64 bit installations of SQL Server essentially does nothing.  Another consideration is that AWE will be [removed in Denali][3].  This removes the question of enabling it or not. 

The better question is, “Should Locked Pages be set?”  [Lock Pages in Memory][4] may help improve performance on 64 bit platforms greatly by preventing memory from being paged out from the buffer pool.  Should you configure lock pages in memory?  If SQL Server is logging sever paged out error, maybe.  There are many things that could cause it.  Other applications installed on the server, large file copies and etc…  Should SQL Server be the only thing installed on the server? Yes.  Sometimes limited resources say otherwise though.

If you see errors containing, “A significant part of sql server process memory has been paged out. This may result in a performance degradation.” in the SQL Server Error Log, you should start you investigation quickly.  Even if the errors were only logged one timespan, the cause must be identified so future performance is not affected.

If working on a preexisting installation and lock pages in memory is not configured, determine if there is a need by using Performance Monitor, monitoring for errors noted in the SQL Server Log and guidance from [this CSS article][5].

**Resources**

[Glenn Berry (Hardware godfather)][6]

[How to reduce paging the buffer pool memory in the 64-bit version of SQL Server][7]

 [Misconceptions – Enable AWE on 64bit SQL Servers][8]

 [1]: http://www.microsoft.com/windowsserver2008/en/us/system-requirements.aspx
 [2]: http://msdn.microsoft.com/en-us/library/ms141766.aspx
 [3]: http://msdn.microsoft.com/en-us/library/ms190673.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms190730.aspx
 [5]: http://blogs.msdn.com/b/psssql/archive/2009/05/12/sql-server-reports-working-set-trim-warning-message-during-early-startup-phase.aspx
 [6]: http://sqlserverperformance.wordpress.com/2011/02/14/sql-server-and-the-lock-pages-in-memory-right-in-windows-server/
 [7]: http://support.microsoft.com/kb/918483
 [8]: http://sqlblog.com/blogs/jonathan_kehayias/archive/2010/10/12/tsql-tuesday-11-misconceptions-enable-awe-on-64bit-sql-servers.aspx