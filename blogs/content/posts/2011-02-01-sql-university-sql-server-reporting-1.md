---
title: SQL University – SQL Server Reporting Services Configuration Files Overview
author: Jes Borland
type: post
date: 2011-02-01T12:01:00+00:00
ID: 1013
excerpt: |
  SQL Server 2008 R2 
  
   
  Welcome to SQL Server Reporting Services week at SQL University. This is a great blog project put together by Jorge Segarra (Twitter | Blog), and contributed to by many SQL professionals. If you aren't a student yet, head over&hellip;
url: /index.php/datamgmt/dbadmin/sql-university-sql-server-reporting-1/
views:
  - 14697
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSRS

---
_SQL Server 2008 R2_ 

<div class="image_block" align="left">
  <a href="/wp-content/uploads/users/grrlgeek/sql-university-shield.png?mtime=1296100411"><img alt="" src="/wp-content/uploads/users/grrlgeek/sql-university-shield.png?mtime=1296100411" width="156" height="174" /></a>
</div>

Welcome to SQL Server Reporting Services week at [SQL University][1]. This is a great blog project put together by Jorge Segarra ([Twitter][2] | [Blog][3]), and contributed to by many SQL professionals. If you aren&#8217;t a student yet, head over to the website and get started! 

This semester, Professor Jes will be guiding you through Reporting Services administration. Let&#8217;s get started! 

**SQL Server Reporting Services Configuration Files**

When you install SQL Server Reporting Services, you will be able to access the most important and most common settings through the Configuration Manager. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRSConfigMgr.JPG?mtime=1296100416"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRSConfigMgr.JPG?mtime=1296100416" width="974" height="735" /></a>
</div>

All of these settings, and many more, are stored in a series of XML configuration files. Understanding what is in these files can help you understand the SSRS architecture better, troubleshoot problems more in-depth, and customize your installations. 

Let’s take a look at what these files are. 

**Where ARE They?**

I see this question posted on forums a lot: where are the configuration files hiding? The default path to these files is C:Program FilesMicrosoft SQL ServerMSRS10_50.MSSQLSERVERReporting Services. There are several folders here, and different config files are in each. 

**RSReportServer** 

Located in the ReportServer folder, this is the meat-and-potatoes of the SSRS files. The settings here are used by Report Manager, the Report Server web service, and background processes. 

Some of the settings you’ll find here include: 

  * Logon credentials
  * Timeout information
  * Authentication methods
  * Email configuration
  * Report rendering and delivery extensions 

My next SQL University post will go more in-depth on the settings on this file. 

**ReportingServicesService** 

This file is located in the ReportServerbin folder. 

Reporting Services has log files, separate from the SQL Server error log files. These are stored at C:Program FilesMicrosoft SQL ServerMSRS10_50.MSSQLSERVERReporting ServicesLogFiles. In this config file, you can edit settings related to these. 

Some of the settings you’ll find here include:
  
• What level of logging you want on your log files.
  
• The name of your log files.
  
• The maximum size of log files.
  
• How log to retain log files. 

This is a great place to show an example of how to modify the config files. Let’s use FileName. By default, it’s ReportServerService_. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/ReportingServicesService Config.JPG?mtime=1296100414"><img alt="" src="/wp-content/uploads/users/grrlgeek/ReportingServicesService Config.JPG?mtime=1296100414" width="478" height="287" /></a>
</div>

My log file folder looks like this:

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/Config File Names A.JPG?mtime=1296100413"><img alt="" src="/wp-content/uploads/users/grrlgeek/Config File Names A.JPG?mtime=1296100413" width="400" height="229" /></a>
</div>

I change the name, save the file, and start the SSRS service: 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/ReportingServicesService Config B.JPG?mtime=1296100413"><img alt="" src="/wp-content/uploads/users/grrlgeek/ReportingServicesService Config B.JPG?mtime=1296100413" width="454" height="283" /></a>
</div>

And my log files are now named differently:

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/Config File Names B.JPG?mtime=1296100412"><img alt="" src="/wp-content/uploads/users/grrlgeek/Config File Names B.JPG?mtime=1296100412" width="389" height="250" /></a>
</div>

**RSSrvPolicy , RSMgrPolicy , and RSPreviewPolicy** 

These three configuration files store security settings for various components. It isn’t recommended that you edit these files, but it’s helpful to know what they correspond to. 

**RSSrvPolicy** &#8211; Report Server. Located at C:Program FilesMicrosoft SQL ServerMSRS10_50.MSSQLSERVERReporting ServicesReportServer
  
**RSMgrPolicy** &#8211; Report Manager.Located at C:Program FilesMicrosoft SQL ServerMSRS10_50.MSSQLSERVERReporting ServicesReportManager
  
**RSPreviewPolicy** &#8211; Report Designer. Note that this is in a different location, as Report Designer is part of Visual Studio (Business Intelligence Development Studio). C:Program Files (x86)Microsoft Visual Studio 9.0Common7IDEPrivateAssemblies

**RSReportDesigner**

This file holds settings for rendering formats and data extensions in the Report Designer. Because it relates to Report Designer, it too is located at C:Program Files (x86)Microsoft Visual Studio 9.0Common7IDEPrivateAssemblies . Microsoft recommends you only modify this file if adding custom extensions. 

**A Word of Caution** 

As with any changes to a system, make sure you back up your files before making changes. Also, be aware that some settings can only be changed from the Configuration Manager, and some are internal and can’t be changed at all. 

My next post will explore ReportingServicesService, giving you information to make your SSRS installations more customized and powerful.

 [1]: http://sqlchicken.com/sql-university/
 [2]: http://twitter.com/sqlchicken
 [3]: http://sqlchicken.com/