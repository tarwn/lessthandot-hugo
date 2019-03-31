---
title: SQL Server 2012 SP1 and in-memory OLTP DB Engine Hekaton announced
author: SQLDenis
type: post
date: 2012-11-07T14:52:00+00:00
excerpt: |
  Microsoft announced a couple of things at the PASS conference today
  
  SQL Server 2012 Service Pack 1 is now available for download, you can get it here: http://www.microsoft.com/en-us/download/details.aspx?id=35575
  
  Just two of the new features avail&hellip;
url: /index.php/datamgmt/datadesign/sql-server-2012-sp1-and/
views:
  - 9821
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Microsoft announced a couple of things at the PASS conference today

SQL Server 2012 Service Pack 1 is now available for download, you can get it here: http://www.microsoft.com/en-us/download/details.aspx?id=35575

Just two of the new features available in this Service Pack

**Selective XML Index**

SQL Server 2012 SP1 introduces a new type of XML index known as a Selective XML Index. This new index can improve querying performance over data stored as XML in SQL Server, allow for much faster indexing of large XML data workloads, and improve scalability by reducing storage costs of the index itself. For more information, see Selective XML Indexes (SXI).

**DBCC SHOW_STATISTICS works with SELECT permission**

In earlier releases of SQL Server, customers need administrative or ownership permissions to run DBCC SHOW_STATISTICS. This restriction impacted the Distributed Query functionality in SQL Server because, in many cases, customers running distributed queries did not have administrative or ownership permissions against remote tables to be able to gather statistics as part of the compilation of the distributed query. While such scenarios still execute, it often results in sub-optimal query plan choices that negatively impact performance. SQL Server 2012 SP1 modifies the permission restrictions and allows users with SELECT permission to use this command. Note that the following requirements exist for SELECT permissions to be sufficient to run the command:

Users must have permissions on all columns in the statistics object

Users must have permission on all columns in a filter condition (if one exists)

Customers using Distributed Query should notice that statistics can now be used when compiling queries from remote SQL Server data sources where they have only SELECT permissions. Trace flag 9485 exists to revert the new permission check to SQL Server 2012 RTM behavior in case of regression in customer scenarios.

All the new SQL Server 2012 Service Pack 1 features can be found here: http://msdn.microsoft.com/en-us/library/bb500435

Download Service Pack 1 for SQL Server 2012 here: http://www.microsoft.com/en-us/download/details.aspx?id=35575

# **Hekaton**

Project codenamed “Hekaton,” a new in-memory technology for transaction processing that will be built directly into the data platform and ship in the next major version of SQL Server.

Currently in private technology preview with a small set of customers, “Hekaton” will complete Microsoft’s portfolio of in-memory capabilities across analytics, transactions, streaming and caching workloads.
  
Based on customer testing to date, “Hekaton” will offer performance gains of up to 10 times for existing apps and up to 50 times for new applications optimized for in-memory performance.
  
We will also announce the next version of our enterprise-class appliance, SQL Server 2012 Parallel Data Warehouse (PDW), available in the first half of 2013.
  
SQL Server 2012 PDW will be powered by PolyBase, a breakthrough data processing engine that will enable queries across relational data and non-relational Hadoop data.
  
Built to tackle customers’ big data challenges, SQL Server 2012 PDW will offer next-generation performance at scale and a redesigned architecture to reduce hardware footprint, all at the lowest total cost in the industry.

The Hekaton announcement can be found here: http://blogs.technet.com/b/dataplatforminsider/archive/2012/11/07/pass-summit-2012-accelerating-business-through-data-insights.aspx

# InfoGraphic summarizing today&#8217;s PASS Keynote

Below is an infographic summarizing today&#8217;s PASS Keynote

<div class="image_block">
  <a href="/wp-content/uploads/users/SQLDenis/sql2012iNFORMATIC1.PNG?mtime=1352311558"><img alt="" src="/wp-content/uploads/users/SQLDenis/sql2012iNFORMATIC1.PNG?mtime=1352311558" width="549" height="659" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/SQLDenis/sql2012iNFORMATIC2.PNG?mtime=1352311567"><img alt="" src="/wp-content/uploads/users/SQLDenis/sql2012iNFORMATIC2.PNG?mtime=1352311567" width="535" height="657" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/SQLDenis/sql2012iNFORMATIC3.PNG?mtime=1352311577"><img alt="" src="/wp-content/uploads/users/SQLDenis/sql2012iNFORMATIC3.PNG?mtime=1352311577" width="539" height="509" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/SQLDenis/sql2012iNFORMATIC4.PNG?mtime=1352311588"><img alt="" src="/wp-content/uploads/users/SQLDenis/sql2012iNFORMATIC4.PNG?mtime=1352311588" width="530" height="625" /></a>
</div>