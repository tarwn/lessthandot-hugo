---
title: FILESTREAM Storage in SQL Server 2008 Technical Article
author: SQLDenis
type: post
date: 2008-10-10T11:54:06+00:00
ID: 171
url: /index.php/datamgmt/datadesign/filestream-storage-in-sql-server-2008-te/
views:
  - 7541
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - filestream
  - sql server 2008

---
Paul Randal has written a very detailed technical article about FILESTREAM Storage in SQL Server 2008.

> This white paper describes the FILESTREAM feature of SQL Server 2008, which allows storage of and efficient access to BLOB data using a combination of SQL Server 2008 and the NTFS file system. It covers choices for BLOB storage, configuring Windows and SQL Server for using FILESTREAM data, considerations for combining FILESTREAM with other features, and implementation details such as partitioning and performance.
> 
> This white paper is targeted at architects, IT Pros, and DBAs tasked with evaluating or implementing FILESTREAM. It assumes the reader is familiar with Windows and SQL Server and has at least a rudimentary knowledge of database concepts such as transactions.

Here is the table of contents:

**Introduction
  
Choices for BLOB Storage
  
Overview of FILESTREAM**
  
&#8211;Dual Programming Model Access to BLOB Data
  
&#8211;When to Use FILESTREAM
  
**Configuring Windows for FILESTREAM**
  
&#8211;Hardware Selection and Configuration
  
&#8211;Physical Storage Layout
  
&#8211;RAID Level Choice
  
&#8211;Drive Interface Choice
  
&#8211;NTFS Configuration
  
&#8212;-Optimizing NTFS Performance
  
&#8212;-Cluster Size
  
&#8212;-Managing Fragmentation
  
&#8212;-Compression
  
&#8212;-Space Management
  
&#8212;-Security
  
&#8211;Antivirus Considerations
  
&#8211;Enabling FILESTREAM in Windows
  
**Configuring SQL Server for FILESTREAM**
  
&#8211;Security Considerations
  
&#8211;Enabling FILESTREAM in SQL Server
  
&#8211;Creating a Database Enabled for FILESTREAM
  
&#8211;Creating a Table for Storing FILESTREAM Data
  
&#8211;Configuring FILESTREAM Garbage Collection
  
&#8211;Partitioning Considerations
  
&#8211;Load Balancing of FILESTREAM Data
  
&#8211;Feature Combinations and Restrictions
  
**Performance Tuning and Benchmarking Considerations
  
Data Migration Considerations
  
FILESTREAM Usage Best Practices
  
Conclusion**

You can find that technical article here: http://msdn.microsoft.com/en-us/library/cc949109.aspx

You also check out Paul Randal's blog here: http://www.sqlskills.com/blogs/paul/