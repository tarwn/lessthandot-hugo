---
title: SQL Server 2008 R2 Editions Pricing Announced
author: SQLDenis
type: post
date: 2009-11-06T12:17:30+00:00
ID: 612
url: /index.php/datamgmt/datadesign/sql-server-2008-r2-editions-pricing-anno/
views:
  - 11878
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - business intelligence
  - gemini
  - madison
  - sql server 2008
  - sql server 2008 r2

---
Microsoft has announced pricing and also what is new in the SQL Server 2008 R2 editions. First here is the pricing

<table cellspacing="10">
  <tr>
    <td>
      <strong>Editions</strong>
    </td>
    
    <td>
      <strong>Per Processor PricingRetail **</strong>
    </td>
    
    <td>
      <strong>Per Server Plus CAL PricingRetail **</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      Parallel Data Warehouse
    </td>
    
    <td>
      $57,498
    </td>
    
    <td>
      Not offered via Server CAL
    </td>
  </tr>
  
  <tr>
    <td>
      Datacenter
    </td>
    
    <td>
      $57,498
    </td>
    
    <td>
      Not offered via Server CAL
    </td>
  </tr>
  
  <tr>
    <td>
      Enterprise
    </td>
    
    <td>
      $28,749
    </td>
    
    <td>
      $13,969 with 25 CALs
    </td>
  </tr>
  
  <tr>
    <td>
      Standard
    </td>
    
    <td>
      $7,499
    </td>
    
    <td>
      $1,849 with 5 CALs
    </td>
  </tr>
</table>

I wonder how many people will go to Datacenter or Parallel Data Warehouse editions? To be fair Sybase IQ charges I believe $40K per core, SQL Server 2008 R2 is priced per socket and would still be a lot cheaper than Sybase IQ

Here are the other details:

## New Premium Editions

_
  
**Datacenter**</p> 

Built on SQL Server 2008 R2 Enterprise, SQL Server 2008 R2 Datacenter is designed to deliver a high-performing data platform that provides the highest levels of scalability for large application workloads, virtualization and consolidation, and management for an organization's database infrastructure. Datacenter helps enable organizations to cost effectively scale their mission-critical environment.

**Key features new to Datacenter:**

Application and Multi-Server Management for enrolling, gaining insights and managing over 25 instances
  
Highest virtualization support for maximum ROI on consolidation and virtualization
  
High-scale complex event processing with SQL Server StreamInsight
  
Supports more than 8 processors and up to 256 logical processors for highest levels of scale
  
Supports memory limits up to OS maximum

**Parallel Data Warehouse**

SQL Server 2008 R2 Parallel Data Warehouse is a highly scalable data warehouse appliance-based solution. Parallel Data Warehouse delivers performance at low cost through a massively parallel processing (MPP) architecture and compatibility with hardware partners – scale your data warehouse from tens and hundreds of terabytes to the petabyte range.

**Key features new to Parallel Data Warehouse:**

10s to 100s TBs to 1+ PB enabled by MPP architecture
  
Advanced data warehousing capabilities like Star Join Queries and Change Data Capture
  
Integration with SSIS, SSRS, and SSAS
  
Supports industry standard data warehousing hub and spoke architecture and parallel database copy

</em>
  


## Investments in Core Editions

This section has all the new stuff in the Enterprise and Standard editions, it claims Standard does backup compression but I believe that is only restore not the backup itself, the following URL does not mention anything about Standard Edition being able to compress backups: http://technet.microsoft.com/en-us/library/bb964719.aspx

> Creating compressed backups is supported only in SQL Server 2008 Enterprise and later, but beginning in SQL Server 2008, every edition can restore a compressed backup.

_**SQL Server 2008 R2 Enterprise**</p> 

SQL Server 2008 R2 Enterprise delivers a comprehensive data platform that provides built-in security, availability, and scale coupled with robust business intelligence offerings—helping enable the highest service levels for mission-critical workloads.

**The following capabilities are new to Enterprise:**

PowerPivot for SharePoint to support the hosting and management of PowerPivot applications in SharePoint
  
Application and Multi-Server Management for enrolling, gaining insights and managing up to 25 instances
  
Master Data Services for data consistency across heterogeneous systems
  
Data Compression now enabled with UCS-2 Unicode support

**SQL Server 2008 R2 Standard**

SQL Server 2008 R2 Standard delivers a complete data management and business intelligence platform for departments and small organizations to run their applications—helping enable effective database management with minimal IT resources.

**The following capabilities are new to Standard:**

Backup Compression to reduce data backups by up to 60% and help reduce time spent on backups *
  
Can be managed instance for Application and Multi-Server Management capabilities

</em>
  
You can find all that stuff on this page here: http://www.microsoft.com/sqlserver/2008/en/us/R2-editions.aspx



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22