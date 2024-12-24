---
title: SSIS:Multi Instance Management made easy.
author: SQLArcher
type: post
date: 2011-03-07T05:13:00+00:00
ID: 1065
excerpt: |
  Some of us work extensively with SSIS and multi-instance SQL clusters. One of the headaches with SSIS in this type of set up, is that SSIS is not cluster aware. This includes where packages are saved when you upload them through SSMS.
   
  Now to give yo&hellip;
url: /index.php/datamgmt/dbadmin/ssis-multi-instance-management-made/
views:
  - 5884
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - SSIS

---
Some of us work extensively with SSIS and multi-instance SQL clusters. One of the headaches with SSIS in this type of set up, is that SSIS is not cluster aware. This includes where packages are saved when you upload them through SSMS.

 

Now to give you the ability to save and manage SSIS packages on specific MSDB databases for a SQL cluster, we need to edited a file. The file can be found at this path:

 

<div class="image_block">
  <a href="/media/users/sqlarcher/SSIS_Post/SSIS Config Path.jpg?mtime=1299482416"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/sqlarcher/SSIS_Post/SSIS Config Path.jpg?mtime=1299482416" alt="" width="652" height="29" /></a>
</div>

 

You can now either scroll down or order the document type in descending order and you will find see this file:

 

<div class="image_block">
  <a href="/media/users/sqlarcher/SSIS_Post/SSIS File.jpg?mtime=1299482416"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/sqlarcher/SSIS_Post/SSIS File.jpg?mtime=1299482416" alt="" width="587" height="26" /></a>
</div>

 

This is the configuration document for the SSIS engine where it provides the connection to MSDB. When you open this, you will see something similar to this:

 

<div class="image_block">
  <a href="/media/users/sqlarcher/SSIS_Post/SSIS Config.jpg?mtime=1299482416"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/sqlarcher/SSIS_Post/SSIS Config.jpg?mtime=1299482416" alt="" width="1014" height="249" /></a>
</div>

 

We are interested in the following node, which we will also edit:

 

<div class="image_block">
  <a href="/media/users/sqlarcher/SSIS_Post/Concerning Factor.jpg?mtime=1299482415"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/sqlarcher/SSIS_Post/Concerning Factor.jpg?mtime=1299482415" alt="" width="360" height="71" /></a>
</div>

 

The highlighted lines will be changed according to the respective settings in your environment. In my example I have a two node active/active cluster, therefor my configuration file will be edited as below:

 

<div class="image_block">
  <a href="/media/users/sqlarcher/SSIS_Post/SSIS Config Edit.jpg?mtime=1299482416"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/sqlarcher/SSIS_Post/SSIS Config Edit.jpg?mtime=1299482416" alt="" width="721" height="154" /></a>
</div>

 

I changed the names from MSDB to Instance1 and 2 to make it easier. This file should be changed on all of the nodes in the cluster and after the change, the SSIS service should be stopped and started through SQL Configuration Manager for the changes to take affect.

For the ServerName value, the value should be changed to _sqlvirtualnamesqlinstancename._ After the all the changes have been made and the service has been restarted we can log into the SSIS engine through Management Studio. Again, because SSIS is not cluster aware, we have to use our SQL virtual name to gain access.

Now we will see the following:

 

<div class="image_block">
  <a href="/media/users/sqlarcher/SSIS_Post/SSIS Folders.jpg?mtime=1299482416"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/sqlarcher/SSIS_Post/SSIS Folders.jpg?mtime=1299482416" alt="" width="445" height="130" /></a>
</div>

Thanks to this handy configuration, all of your SSIS packages can be saved to their respective instance MSDBs' and management will be easier due to a centralized access point and no need of guessing where your packages are being saved.