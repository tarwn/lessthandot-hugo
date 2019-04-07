---
title: Do you care that SQL Server Denali cannot be installed on Windows XP?
author: SQLDenis
type: post
date: 2011-06-15T11:54:00+00:00
ID: 1221
excerpt: |
  Dan Jones posted a blogpost here: http://blogs.msdn.com/b/dtjones/archive/2011/06/10/sql-server-code-name-denali-supported-oses-and-upgrade-paths.aspx showing what OS Denali can be installed on.
  
  
  1) The current support matrix for OSes is as follows:&hellip;
url: /index.php/datamgmt/datadesign/do-you-care-that-sql/
views:
  - 5780
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - denali
  - sql server
  - windows 7
  - windows xp

---
Dan Jones posted a blogpost here: http://blogs.msdn.com/b/dtjones/archive/2011/06/10/sql-server-code-name-denali-supported-oses-and-upgrade-paths.aspx showing what OS Denali can be installed on.

_1) The current support matrix for OSes is as follows:
   
Windows Vista SP2 or later
   
Windows Server 2008 SP2 or later
   
Windows Server 2008 R2 SP1 or later
   
Windows 7 SP1 or later</p> 

2) Denali will support upgrading from these SQL Server versions:
   
SQL Server 2005 SP4 or later
   
SQL Server 2008 SP2 or later
   
SQL Server 2008 R2 SP1 or later</em>

As you can see there is no Windows XP on that list. I am fine with that for a couple of reasons

1) Denali is not out yet, it might be another year before it goes RTM, and even if it is released a good number of companies wait for service pack 1. By the time that happens a lot of those XP machines might be running Windows 7

2) Nothing prevents you from running a Windows 7 virtual instance on your XP box so that you can still do your work

3) There is always Query Analyzer, Query Analyzer should connect to Denali without a problem, it connects to 2008 R2 just fine

So what do you think, will this be a roadblock for you?