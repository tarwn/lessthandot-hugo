---
title: SQL Server 2014 CTP1 Available for download
author: SQLDenis
type: post
date: 2013-06-25T07:58:00+00:00
ID: 2110
excerpt: |
  Microsoft has made available for download CTP1 of SQl Server 2014
  
  Be aware of the following important information
  
  
    Installing side-by-side with other versions of SQL Server is NOT supported.
  
  
    Upgrade from other versions of SQL Server is N&hellip;
url: /index.php/datamgmt/dbprogramming/mssqlserver/sql-server-2014-ctp1-available/
views:
  - 13378
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - hekaton
  - sql server 2014

---
Microsoft has made available for download CTP1 of [SQl Server 2014][1]

Be aware of the following important information

  * Installing side-by-side with other versions of SQL Server is NOT supported.
  * Upgrade from other versions of SQL Server is NOT supported.
  * Only use the SQL Server Management Studio that ships as a part of this build to manage this build. This build does not work with or support side-by-side installations with any client redistributables of SQL Server such as Data Tools, Data Tier Application Framework, etc..
  * Side-by-side installations of this build with Visual Studio 2012 or earlier versions is NOT supported.

If you have SQL Server or Visual Studio installed on your machine, the installer will throw a check error and will not proceed with the install. Use a virtual machine to test this build instead of uninstalling Visual Studio and SQL Server on your machine

Running `@@version` will give you the following

Microsoft SQL Server 2014 (CTP1) &#8211; 11.0.9120.5 (X64)
	  
Jun 10 2013 20:09:10
	  
Copyright (c) Microsoft Corporation
	  
Enterprise Evaluation Edition (64-bit) on Windows NT 6.2 <x64> (Build 9200: )

Here is what the SSMS splash screen looks like
  
[<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/SSMS2014Startup.PNG?mtime=1371999115" width="467" height="306" />][2]

Clicking on Help&#8211;> About gives the following info

<pre>Microsoft SQL Server Management Studio		11.0.9120.5
Microsoft Analysis Services Client Tools	11.0.9120.5
Microsoft Data Access Components (MDAC)		6.2.9200.16384
Microsoft MSXML					3.0 6.0 
Microsoft Internet Explorer			9.10.9200.16599
Microsoft .NET Framework			4.0.30319.18046
Operating System				6.2.9200</pre>

You can download SQL Server 2014 CTP1 here: http://technet.microsoft.com/en-US/evalcenter/dn205292

I ran into an issue, the installer at the end popped up the following message: **Wait on the Database Engine recovery handle failed. Check the SQL Server error log for potential causes.**

There was no problem starting SQl Server manually, I wonder if anyone else will run into this issue

Here is some of the error log info

> Overall summary:
    
> Final result: Failed: see details below
    
> Exit code (Decimal): -2061893606
    
> Start time: 2013-06-22 08:11:24
    
> End time: 2013-06-22 08:25:35
    
> Requested action: Install
> 
> Setup completed with required actions for features.
  
> Troubleshooting information for those features:
    
> Next step for SQLEngine: Use the following information to resolve the error, uninstall this feature, and then run the setup process again.
> 
> Feature: Database Engine Services
    
> Status: Failed: see logs for details
    
> Reason for failure: An error occurred during the setup process of the feature.
    
> Next Step: Use the following information to resolve the error, uninstall this feature, and then run the setup process again.
    
> Component name: SQL Server Database Engine Services Instance Features
    
> Component error code: 0x851A001A
    
> Error description: Wait on the Database Engine recovery handle failed. Check the SQL Server error log for potential causes.
    
> Error help link: [Error link][3]
> 
> Feature: SQL Browser
    
> Status: Passed
> 
> Feature: SQL Writer
    
> Status: Passed
> 
> Rules with failures:
> 
> Global rules:
> 
> Scenario specific rules:
> 
> Rules report file: C:Program FilesMicrosoft SQL Server110Setup BootstrapLog20130622\_080701SystemConfigurationCheck\_Report.htm 

Clicking on that link brings you to the following

_Details
  
ID: 50000
  
Source: setup.rll </p> 

We&#8217;re sorry
  
There is no additional information about this issue in the Error and Event Log Messages or Knowledge Base databases at this time. You can use the links in the Support area to determine whether any additional information might be available elsewhere.</em>

Download the [SQL Server 2014 CTP1][1] and start playing with Hekaton
  
Make sure to check out Kalen delaney&#8217;s Hekaton whitepaper as well, you can download the whitepaper here: http://t.co/6IzfRnJLcu</x64>

Here are a couple of small posts I put together

[SQL Server 2014 CTP1 native compiled Hekaton procedures are faster than regular procedures][4]
  
[Supported and unsupported datatypes with Hekaton tables][5]
  
[New Dynamic Management Views in SQL Server 2014 CTP1][6]

 [1]: http://technet.microsoft.com/en-US/evalcenter/dn205292
 [2]: /wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/SSMS2014Startup.PNG?mtime=1371999115
 [3]: http://go.microsoft.com/fwlink?LinkId=20476&ProdName=Microsoft+SQL+Server&EvtSrc=setup.rll&EvtID=50000&ProdVer=11.0.9120.5&EvtType=0xD15B4EB2%400x4BDAF9BA%401306%4026&EvtType=0xD15B4EB2%400x4BDAF9BA%401306%4026
 [4]: /index.php/DataMgmt/DBProgramming/sql-server-2014-ctp1-native
 [5]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/supported-and-unsupported-datatypes-with
 [6]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/new-dynamic-management-views-in