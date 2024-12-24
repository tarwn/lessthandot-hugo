---
title: Setting up a Central Management Server
author: Kevin Conan
type: post
date: 2012-06-29T11:32:00+00:00
ID: 1661
excerpt: |
  This is going to be the start of a series on managing multiple SQL Instances.  Up to now, I've been mostly writing about Idera's Diagnostic Manager which is great for this purpose, but like anything else, multiple tools are needed.
  
  Do you ever find y&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/setting-up-a-central-management/
views:
  - 9785
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - cms
  - sql server

---
This is going to be the start of a series on managing multiple SQL Instances. Up to now, I've been mostly writing about Idera's Diagnostic Manager which is great for this purpose, but like anything else, multiple tools are needed.

Do you ever find yourself deploying the same code to the same 10 SQL Instances one at a time and wish there was a better way? Do you keep track of what SQL Instances you have by writing them on Post-It Notes in your cube? Are your developers always asking you what the names of the SQL Instances are?

If you answered yes to any of those questions, then you need to setup a Central Management Server! (CMS for short)

A Central Management Server is basically like a phone book (or a contact list on a phone for you kids too young to remember phone books!). The other thing CMS allows you to do, is to connect to multiple instances and run a query against all of them from a single SSMS window.

Setting up a CMS is really easy! 

First thing to do is pick a SQL Instance that you want to host the CMS (you do not need a dedicated instance for it). A few things to consider is that the CMS is stored in the msdb, so db_reader (updated thanks to SQLArcher) or ServerGroupReaderRole access is needed for anyone you want to see it. The amount of disk space needed is very little (less than 100MB). To plan ahead, choose an instance where the service account that runs the job agent can be given access to all the instances you will list in the CMS (for policy management). Next, click open SSMS and connect to the chosen instance.

From the View Menu, choose Registered Servers. Expand the Database Engine and then right click on Central Management Servers and choose the only option you get which is "Register Central Management Server...".

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/CMS1.jpg?mtime=1340976654"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/CMS1.jpg?mtime=1340976654" width="656" height="526" /></a>
</div>

In the Server name, enter the SQL Instance you chose to host the CMS, set Authentication to whatever you shop uses (we use Windows Authentication), in Registered server name you can put in a nickname for the CMS and then you can fill out the description if you want.

Now if you right click on your CMS, you choose New Server Group to create folders to organize your instances. To add instances, you would choose New Server Registration and fill it out like you did when you added the CMS.

Here is a sample of how you could make it look:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/CMS2.jpg?mtime=1340976654"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/kconan/CMS2.jpg?mtime=1340976654" width="265" height="155" /></a>
</div>

If you right click on a folder you can choose New Query which will open a new window that is connected to all the instances in that folder including the children folders).

If you want to give Developers access to this feature, you will need to set them up with db_datareader (updated thanks to SQLArcher) or ServerGroupReaderRole access to the msdb database.