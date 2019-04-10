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
  
–Dual Programming Model Access to BLOB Data
  
–When to Use FILESTREAM
  
**Configuring Windows for FILESTREAM**
  
–Hardware Selection and Configuration
  
–Physical Storage Layout
  
–RAID Level Choice
  
–Drive Interface Choice
  
–NTFS Configuration
  
—-Optimizing NTFS Performance
  
—-Cluster Size
  
—-Managing Fragmentation
  
—-Compression
  
—-Space Management
  
—-Security
  
–Antivirus Considerations
  
–Enabling FILESTREAM in Windows
  
**Configuring SQL Server for FILESTREAM**
  
–Security Considerations
  
–Enabling FILESTREAM in SQL Server
  
–Creating a Database Enabled for FILESTREAM
  
–Creating a Table for Storing FILESTREAM Data
  
–Configuring FILESTREAM Garbage Collection
  
–Partitioning Considerations
  
–Load Balancing of FILESTREAM Data
  
–Feature Combinations and Restrictions
  
**Performance Tuning and Benchmarking Considerations
  
Data Migration Considerations
  
FILESTREAM Usage Best Practices
  
Conclusion**

You can find that technical article here: http://msdn.microsoft.com/en-us/library/cc949109.aspx

You also check out Paul Randal's blog here: http://www.sqlskills.com/blogs/paul/