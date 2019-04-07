---
title: Microsoft SQL Server, codename “Denali”, will be the last release to support OLE DB, ODBC is the new new thing
author: SQLDenis
type: post
date: 2011-08-30T16:13:00+00:00
ID: 1304
excerpt: |
  According to the Microsoft SQLNCli team blog, SQL Server, codename "Denali", will be the last release to support OLE DB.
  
  Here is what they are currently saying
  
  The next release of Microsoft SQL Server, codename “Denali”, will be the last release t&hellip;
url: /index.php/datamgmt/datadesign/microsoft-sql-server-codename-denali/
views:
  - 11007
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - denali
  - odbc
  - ole db
  - sql server
  - sql server denali

---
According to the Microsoft SQLNCli team blog, SQL Server, codename &#8220;Denali&#8221;, will be the last release to support OLE DB.

Here is what they are currently saying

> The next release of Microsoft SQL Server, codename “Denali”, will be the last release to support OLE DB. OLE DB will be supported for 7 years from launch, the life of Denali support, to allow you a large window of opportunity for changing your applications before the deprecation. This deprecation applies to the Microsoft SQL Server OLE DB provider only. Other OLE DB providers as well as the OLE DB standard will continue to be supported until explicitly announced.
> 
> We encourage you to adopt ODBC in the development of your new and future versions of your application. You don’t need to change your existing applications using OLE DB, as they will continue to be supported for seven years from the launch of Denali, but you may want to consider migrating those applications to ODBC as a part of your future roadmap. 

And

> ODBC is the de-facto industry standard for native relational data access, which is supported on all platforms including SQL Azure. Cloud is universal and in order to support all client applications connecting from any platform to the cloud, Microsoft has been fully aligned with ODBC on SQL Azure, as ODBC is the only set of APIs that are available on all platforms including non-Windows platforms.

Mmmm..it looks like ODBC is back from the dead&#8230;&#8230;.

What do you think&#8230;how will this impact you? Leave a comment if this affects you or if you have been using ODBC all along

More details including information on how to migrate your applications can be found here: http://blogs.msdn.com/b/sqlnativeclient/archive/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access.aspx