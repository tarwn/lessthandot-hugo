---
title: Fixing a SQL Server could not spawn FRunCM thread error
author: SQLDenis
type: post
date: 2013-04-30T12:28:00+00:00
excerpt: |
  I had to change the account that some SQL Server boxes were using to run the SQL Server services. After I changed the account on one machine from DomainNameUser to DomainNameNewUser SQL Server wouldn't start up
  
  In the error log I saw the following&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/fixing-a-sql-server-could/
views:
  - 58924
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - encryption
  - error
  - fix
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
I had to change the domain account on some SQL Server boxes that we have. After I changed the account on one machine from DomainNameUser to DomainNameNewUser SQL Server wouldn&#8217;t start up

In the error log I saw the following messages

> Server Error: 17190, Severity: 16, State: 1.
  
> 2013-04-30 12:42:06.11 Server Initializing the FallBack certificate failed with error code: 1, state: 1, error number: -2146893802.
  
> 2013-04-30 12:42:06.11 Server Unable to initialize SSL encryption because a valid certificate could not be found, and it is not possible to create a self-signed certificate.
  
> 2013-04-30 12:42:06.11 Server Error: 17182, Severity: 16, State: 1.
  
> 2013-04-30 12:42:06.11 Server TDSSNIClient initialization failed with error 0x80092004, status code 0x80. Reason: Unable to initialize SSL support. Cannot find object or property.
> 
> 2013-04-30 12:42:06.11 Server Error: 17182, Severity: 16, State: 1.
  
> 2013-04-30 12:42:06.11 Server TDSSNIClient initialization failed with error 0x80092004, status code 0x1. Reason: Initialization failed with an infrastructure error. Check for previous errors. Cannot find object or property.
> 
> 2013-04-30 12:42:06.11 Server Error: 17826, Severity: 18, State: 3.
  
> 2013-04-30 12:42:06.11 Server Could not start the network library because of an internal error in the network library. To determine the cause, review the errors immediately preceding this one in the error log.
  
> 2013-04-30 12:42:06.11 Server Error: 17120, Severity: 16, State: 1.
  
> 2013-04-30 12:42:06.11 Server SQL Server could not spawn FRunCM thread. Check the SQL Server error log and the Windows event logs for information about possible related problems.

We are not using encryption, VIA is disabled as well. After trying all kinds of different things I notice that when logging on with this profile on the box there was an error. Something about that the profile couldn&#8217;t be created and a temporary profile would be used instead

In the end I had to wipe out the user profile from the box

You do that by following these steps

Right click on computer, select Properties
  
Click on Advanced System Settings
  
Select the Advanced Tab
  
On the user profiles section click on Settings

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/UserProfile.PNG?mtime=1367323923"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/UserProfile.PNG?mtime=1367323923" width="399" height="451" /></a>
</div>

Select the profile and hit Delete

If you try to log in as that user, the profile will be recreated.

Now when I tried to start SQL Server again, the errors were not there and SQL Server started up without a problem