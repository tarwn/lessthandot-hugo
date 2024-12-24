---
title: How to read the error log on an Amazon RDS SQL Server instance
author: SQLDenis
type: post
date: 2013-03-21T11:21:00+00:00
ID: 2047
excerpt: |
  Earlier today, i created an Amazon RDS SQL Server instance, you can check out that post here: Trying out Amazon Relational Database Service
  I decided to take a closer look, the first thing I wanted to know is if I could see the error log. There are two&hellip;
url: /index.php/datamgmt/dbadmin/how-to-read-the-error/
views:
  - 27454
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin
tags:
  - amazon web services
  - aws
  - cloud
  - rds

---
Earlier today, I created an Amazon RDS SQL Server instance, you can check out that post here: [Trying out Amazon Relational Database Service][1]
  
I decided to take a closer look, the first thing I wanted to know is if I could see the error log. 

The first thing I did was check if I could see the logs from SSMS, I expanded the error log folder and was greeted with this message

> Failed to retrieve data for this request. (Microsoft.SqlServer.Management.Sdk.Sfc)
  
> An exception occurred while executing a Transact-SQL statement or batch. (Microsoft.SqlServer.ConnectionInfo)
  
> Only members of the securityadmin role can execute this stored procedure. (Microsoft SQL Server, Error: 15003)

I decided to investigate further, there are two ways to see the error log. One way is to use the AWS RDS Dashboard. Click on the DB Instance that you are interested in and you will see a tab named Logs.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/AWS/AwsErrorLog1.PNG?mtime=1363863573"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/AWS/AwsErrorLog1.PNG?mtime=1363863573" width="752" height="446" /></a>
</div>

Once you click on the view button, you will see something like this

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/AWS/AwsErrorLog2.PNG?mtime=1363863585"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Denis/AWS/AwsErrorLog2.PNG?mtime=1363863585" width="628" height="408" /></a>
</div>

That is all nice and dandy but can I do it from T-SQL? Let's find out by running xp_readerrorlog.

```sql
EXEC master.dbo.xp_readerrorlog
```

Here is the output
  
Msg 229, Level 14, State 5, Procedure xp_readerrorlog, Line 1
  
The EXECUTE permission was denied on the object 'xp_readerrorlog', database 'mssqlsystemresource', schema 'sys'.

That is no good. Luckily for us there is a stored procedure that is provided, this proc is named rds\_read\_error_log 

Here is how you execute it

```sql
EXEC rdsadmin..rds_read_error_log 
```

The output is something like you expect from xp_readerrorlog

> 2013-03-21 13:04:08.100 Server Microsoft SQL Server 2012 – 11.0.2100.60 (X64)
	  
> Feb 10 2012 19:39:15
	  
> Copyright (c) Microsoft Corporation
	  
> Express Edition (64-bit) on Windows NT 6.1 <x64> (Build 7601: Service Pack 1) (Hypervisor)
  
> 
  
> 2013-03-21 13:04:08.100 Server (c) Microsoft Corporation.
  
> 2013-03-21 13:04:08.100 Server All rights reserved.
  
> 2013-03-21 13:04:08.100 Server Server process ID is 2252.
  
> 2013-03-21 13:04:08.100 Server System Manufacturer: 'Xen', System Model: 'HVM domU'.
  
> 2013-03-21 13:04:08.100 Server Authentication mode is MIXED.
  
> 2013-03-21 13:04:08.100 Server Logging SQL Server messages in file 'D:RDSDBDATALogERROR'.
  
> 2013-03-21 13:04:08.100 Server The service account is 'RDSGroupRDSIMAGE$'. This is an informational message; no user action is required.
  
> 2013-03-21 13:04:08.100 Server Registry startup parameters:
	   
> -d D:RDSDBDATADATAmaster.mdf
	   
> -e D:RDSDBDATALogERROR
	   
> -l D:RDSDBDATADATAmastlog.ldf
	   
> -k 20.000000
	   
> -T 3226
	   
> -T 7806
  
> 2013-03-21 13:04:08.100 Server Command Line Startup Parameters:
	   
> -s "MSSQLSERVER"
  
> 2013-03-21 13:04:09.850 Server SQL Server detected 1 sockets with 1 cores per socket and 1 logical processors per socket, 1 total logical processors; using 1 logical processors based on SQL Server licensing. This is an informational message; no user action is required.
  
> 2013-03-21 13:04:09.850 Server SQL Server is starting at normal priority base (=7). This is an informational message only. No user action is required.  
> </x64>

Here is what the stored procedure signature looks like
  


```sql
CREATE PROCEDURE [dbo].[rds_read_error_log] @index INT = 0, @type INT = 1,
@search_str1 VARCHAR(255) = NULL, @search_str2 VARCHAR(255) = NULL,
@start_time DATETIME = NULL, @end_time DATETIME = NULL,
@sort_order NVARCHAR(4) = N'asc'  
```

From the docs

> The first two parameters are important when you call the rdsadmin.dbo.rds\_read\_error_log stored procedure:
> 
> The @index parameter indicates the log that Amazon RDS will read from. The default value of 0 indicates the current log is used. A value of 1 indicates that the previously rotated log is used.
> 
> The @type parameter indicates which type of log is read. The default value of 1 indicates that the SQL Server Error Log is used. A value of 2 indicates that the SQL Server Agent Log is used. All the other parameters are related to searching and sorting results and they can be kept at their default values. 

So there you have it, a nice and easy way to check the log by running the `rdsadmin..rds_read_error_log` stored procedure

 [1]: /index.php/DataMgmt/DBProgramming/trying-out-amazon-relational-database