---
title: Cumulative Update 1 to the RML Utilities for Microsoft SQL Server Released
author: SQLDenis
type: post
date: 2008-11-12T18:44:11+00:00
ID: 203
url: /index.php/datamgmt/datadesign/cumulative-update-1-to-the-rml-utilities/
views:
  - 3332362
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql server 2008

---
**What Are the RML Utilities?** 

<font face="Calibri" size="2">The Microsoft SQL Server support team uses several internally written utilities to ease the work that is related to a typical customer support case.&#160; One such utility suite is the Replay Markup Language (RML) Utilities for SQL Server.&#160; Database developers and system administrators can use the RML Utilities to work with Microsoft SQL Server 2000, Microsoft SQL Server 2005 and Microsoft SQL Server 2008. </font>

<font face="Calibri" size="2">You can use the RML Utilities for SQL Server to perform the following tasks: </font>

  * <font face="Calibri" size="2">You can determine the application, the database, the SQL Server login, or the query that is using the most resources.</font> 
  * <font face="Calibri" size="2">You can determine whether the execution plan for a batch is changed when you capture the trace for the batch.&#160; Additionally, you can use the RML Utilities for SQL Server to determine how SQL Server performs each of these execution plans.</font> 
  * <font face="Calibri" size="2">You can determine the queries that are running slower than before. </font>

<font face="Calibri" size="2">After you capture a trace for an instance of SQL Server, you can use the RML Utilities for SQL Server to replay the trace file against another instance of SQL Server. If you also capture the trace during the replay, you can use the RML Utilities for SQL Server to compare the new trace file to the original trace file. You can use this technique to test how SQL Server behaves after you apply changes. For example, you can use this technique to test how SQL Server behaves after you do the following: </font>

  * <font face="Calibri" size="2">You install a SQL Server service pack.</font> 
  * <font face="Calibri" size="2">You install a SQL Server hotifx.</font> 
  * <font face="Calibri" size="2">You update a stored procedure or a function.</font> 
  * <font face="Calibri" size="2">You update an index or create an index. </font>

<font face="Calibri" size="2">The RML Utilities are very useful if you want to simulate application testing when it is impractical or impossible to test by using the real application.&#160; For example, in a test environment, it may be difficult to generate the same user load that exists in the production environment.&#160; You can use the RML Utilities to replay a production workload in a test environment and assess the performance impact of changes such as an upgrade to SQL Server 2008 or application of a SQL Server service pack.&#160; Additionally you can use the RML Utilities to analyze and compare various replay workloads.&#160; This kind of regression analysis would otherwise be a difficult process that you would have to perform manually.</font>

Version 9.01 of the RML Utilities for Microsoft SQL Server has been released. This release of the RML Utilities provides support for SQL Server 2000, SQL Server 2005 and SQL Server 2008. Additionally this release of the RML Utilities for SQL Server contains important software updates, enhanced features and reports, and performance and scalability improvements.

To download the current web release of the RML Utilities for SQL Server visit the following Microsoft Web site:

RML Utilities for SQL Server (x86) &#8211; http://www.microsoft.com/downloads/details.aspx?FamilyID=7edfa95a-a32f-440f-a3a8-5160c8dbe926

RML Utilities for SQL Server (x64) &#8211; http://www.microsoft.com/downloads/details.aspx?FamilyID=b60cdfa3-732e-4347-9c06-2d1f1f84c342