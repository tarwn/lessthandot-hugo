---
title: SQL Azure CTP Testers Can Upgrade Their Accounts To Paid Commercial Subscriptions Starting Today
author: SQLDenis
type: post
date: 2010-01-04T17:17:39+00:00
url: /index.php/datamgmt/datadesign/sql-azure-ctp-testers-can-upgrade-their/
views:
  - 4533
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - azure
  - cloud computing
  - sql azure

---
According to a post on the [SQL Azure Team Blog][1] you can upgrade your CTP accounts to a paid commercial subscription starting today, below is an excerpt from that post

> Starting today you will be able to upgrade your Community Technology Preview (CTP) account to a paid commercial subscriptions. If you upgrade your CTP accounts during the month of January, 2010, all Windows Azure platform usage incurred during this month will be at no charge. You will also have full visibility during this month to your Windows Azure platform usage. Billing and SLAs for all commercial accounts will begin on February 1st, 2010

Pricing is also announced, here is for example the Windows Azure Platform Introductory Special


_ 

**Included each month at no charge:**

  * Windows Azure
  * 25 hours of a small compute instance
  * 500 MB of storage
  * 10,000 storage transactions

  * SQL Azure
  * 1 Web Edition database (available for first 3 months only)

  * AppFabric
  * 100,000 Access Control and Service Bus message operations*

  * Data Transfers (per region**)
  * 500 MB in
  * 500 MB out

**Standard Rates:**

Windows Azure

  * Compute
  * Small instance (default): $0.12 per hour
  * Medium instance: $0.24 per hour
  * Large instance: $0.48 per hour
  * Extra large instance: $0.96 per hour

  * Storage
  * $0.15 per GB stored per month
  * $0.01 per 10,000 storage transactions

  * Content Delivery Network (CDN)
  * Service currently available as a Community Technology Preview (CTP) at no charge

SQL Azure

  * Web Edition – Up to 1 GB relational database
  * $9.99 per database per month

  * Business Edition – Up to 10 GB relational database
  * $99.99 per database per month 

AppFabric

  * Access Control
  * $0.15 per 100,000 message operations*

  * Service Bus
  * $0.15 per 100,000 message operations*

Data Transfers

  * North America and Europe** regions
  * $0.10 per GB in
  * $0.15 per GB out

  * Asia Pacific** Region
  * $0.30 per GB in
  * $0.45 per GB out

  * Inbound data transfers during off-peak times through June 30, 2010 are at no charge. Prices revert to our normal inbound data transfer rates after June 30, 2010.

</em>

You can get all the other pricing information here: http://www.microsoft.com/windowsazure/offers/

Now SQL Azure makes sense for some people and for some people it does not. In my case it does not since my databases are much bigger than what SQL Azure currently supports.

If you are thinking of migrating to SQL Azure I encourage you to listen to the following podcast on dotnetrocks: [Huey and Wegner Migrate Us to SQL Azure][2]. in this podcast a lot of things that will not work in SQL Azure but do work in SQL Server will be examined.

**SQL Azure Migration Wizard** 
  
Before you migrate to SQL Azure for SQL Server I encourage you to check out the SQL Azure Migration Wizard. Here is what the SQL Azure Migration Wizard does

The SQL Azure Migration Wizard helps you migrate your local SQL Server 2005 / 2008 databases into SQL Azure. The wizard walks you through the selection of your SQL objects, creates SQL scripts suitable for SQL Azure, and allows you to migrate your data.

**Project Details**  
The SQL Azure Migration Wizard (SQLAzureMW) gives you the options to analyzes, generates scripts, and migrate data (via BCP) from: 

  1. SQL Server to SQL Azure
  2. SQL Azure to SQL Server
  3. SQL Azure to SQL Azure

It will also analyze SQL Profiler trace files and TSQL script for compatibility issues with SQL Azure. 

  1. If your source is a SQL Server database, SQLAzureMW will list all of the object types (i.e. Tables, Stored Procedures, Views, etc.) and let you decide which ones you want analyzed / scripted. Using the “Advanced” options you can tell SQLAzureMW which compatibility checks to perform and if the data should be migrated.
  2. If your source is a file containing TSQL, then you will be given the option to have SQLAzureMW check the TSQL for incompatibilities and fix where possible or just run the script without any compatibility checking.
  3. You can specify a SQL Profiler trace file for analysis.



You can download the SQL Azure Migration Wizard on CodePlex here: http://sqlazuremw.codeplex.com/

\*** **Remember, if you have a SQL related question try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://blogs.msdn.com/ssds/archive/2010/01/04/9943474.aspx
 [2]: http://www.dotnetrocks.com/default.aspx?showNum=512
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22