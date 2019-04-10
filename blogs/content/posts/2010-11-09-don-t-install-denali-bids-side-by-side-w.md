---
title: Don’t install Denali BIDS side by side with a 2008 instance
author: SQLDenis
type: post
date: 2010-11-09T16:28:22+00:00
ID: 937
excerpt: 'If you already have a SQL Server 2008 or a SQL Server 2008 R2 instance then do not install SQL Server Denali as a named instance. If you do so then your current SSIS development environment will be broken. There is a reason there are virtual machines, I&hellip;'
url: /index.php/datamgmt/datadesign/don-t-install-denali-bids-side-by-side-w/
views:
  - 11302
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - bids
  - ctp
  - database
  - sql server denali
  - ssis

---
If you already have a SQL Server 2008 or a SQL Server 2008 R2 instance then do not install SQL Server Denali as a named instance. If you do so then your current SSIS development environment will be broken. There is a reason there are virtual machines, I recommend using [VirtualBox][1]

With CTP1 of Denali, BIDS is still based on Visual Studio 2008, in a later version it will use the 2010 shell.

Anyway if you do install Denali as a named instance this is what happens when you try to create a new SSIS project

[<img src="http://farm2.static.flickr.com/1079/5151504623_2289d67254.jpg" width="481" height="201" alt="Denali SSIS Error" />][2]

Here is the text

—————————
  
Microsoft Visual Studio
  
—————————
  
'C:UsersDenisAppDataLocalTemptemp.dtproj' cannot be opened because its project type (.dtproj) is not supported by this version of the application. 

To open it, please use a version that supports this type of project.
  
—————————
  
OK Help
  
—————————

Here is also a small video I created [video:youtube:hdNeTWHiwW0] 

Click on the [SQL Server Denali][3] tag to see all our SQL Server Denali related posts

 [1]: http://www.virtualbox.org/
 [2]: http://www.flickr.com/photos/denisgobo/5151504623/ "Denali SSIS Error by Denis Gobo, on Flickr"
 [3]: /index.php/All/sql+server+denali: