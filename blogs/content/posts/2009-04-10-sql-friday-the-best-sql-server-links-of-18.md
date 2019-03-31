---
title: SQL Friday, The Best SQL Server Links Of The Past Week Episode 17
author: SQLDenis
type: post
date: 2009-04-10T13:21:32+00:00
url: /index.php/datamgmt/datadesign/sql-friday-the-best-sql-server-links-of-18/
views:
  - 5074
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql friday

---
Time for another episode of the **SQL Friday, The Best SQL Server Links Of The Past Week** show, the Good Friday edition.
  
Here is what I found interesting this past week in SQL Land:

**[Updated Errors may occur after configuring Analysis Services to use Kerberos authentication on Advanced Encryption Standard Aware Operating Systems][1]**
  
The PSS SQL Server Engineers explain what causes this, here is just a part of the post

> The Microsoft SQL Server Analysis Services support team has seen an increasing number of issues involving errors when attempting to execute queries against or deploy databases to instances of Analysis Services 2005 and Analysis Services 2008 that are configured for Kerberos authentication and running on Windows 2008 Server or Windows Vista. This note provides information regarding the errors that have been reported and investigated.
> 
> What we&#8217;ve found is that when a client application is running on an operating system that is Advanced Encryption Standard (AES) aware (i.e. Windows Vista and Windows 2008 Server) and connects to an Analysis Services server that is running on an operating system that is AES aware then one of the following error return values or error messages may surface.
> 
> 0X80090302
  
> SEC\_E\_NOT_SUPPORTED
  
> The requested Function is not supported
> 
> 0x8009030f
  
> SEC\_E\_MESSAGE_ALTERED
  
> The message or signature supplied for verification has been altered

**[Free SQL Server DBA Training Videos][2]**
  
Brent Ozar shows us some links to **SQL Server DBA Tutorial Podcasts** and also **Free Online SQL Server Training Events**

**[The 3 Things you Need to Know to Install SQL 2005 on Windows 2008 Cluster][3]**
  
The PSS SQL Server Engineers explain what we need to know to install SQL Server 2005 on a Windows 2008 Cluster

**[Page Splitting & Rollbacks][4]**
  
Michelle Ufford shows us that when you have a lot of rollbacks you could have page splits and not be aware of it

**[SQL Server 2008 SP1 and Cumulative Updates Explained][5]**
  
Does SQL Server 2008 SP1 contain the hotfixes that were included in Cumulative Update 4? good question, the PSS SQL Server Engineers explain that SQL Server 2008 SP1 does contain some fixes in CU4 for SQL Server 2008 RTM. There is a table in the post so make sure that you take a look



That is it for this week, I will tag the weekly posts with **SQL Friday** in case you want to see the whole archive in the future

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][6] forum or our [Microsoft SQL Server Admin][7] forum**<ins></ins>

 [1]: http://blogs.msdn.com/psssql/archive/2009/04/03/errors-may-occur-after-configuring-analysis-services-to-use-kerberos-authentication-on-advanced-encryption-standard-aware-operating-systems.aspx
 [2]: http://feedproxy.google.com/~r/BrentOzar-SqlServerDba/~3/OGJVyaL6OF8/
 [3]: http://blogs.msdn.com/psssql/archive/2009/04/08/the-3-things-you-need-to-know-to-install-sql-2005-on-windows-2008-cluster.aspx
 [4]: http://feedproxy.google.com/~r/SqlFool/~3/Xlk6dqsXROM/
 [5]: http://blogs.msdn.com/psssql/archive/2009/04/09/sql-server-2008-sp1-and-cumulative-updates-explained.aspx
 [6]: http://forum.ltd.local/viewforum.php?f=17
 [7]: http://forum.ltd.local/viewforum.php?f=22