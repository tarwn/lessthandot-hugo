---
title: Copy SQL Server Database
author: Ted Krueger (onpnt)
type: post
date: 2009-04-13T11:13:07+00:00
ID: 383
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/copy-sql-server-database/
views:
  - 16018
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
With the type of databases that can be set to nightly or even weekly recovery points, the copy database task is ideal and a quick way to setup you DR services.

Many times the copy database task is overlooked for tasks such as disaster/recovery and test or development resources. This task available is very useful for databases that primarily hold meta data or are relatively small in size. The company I am with now has 3 primary third party installations that have databases in size ranging from 500MB to only 2GB. These are meta data and logging databases installed with the third party applications. It's almost not worth the resources and monitoring efforts to have these databases setup for log shipping, mirroring etc.. for DR purposes. The databases only consist of configurations for the software and key recovery points rarely change.

There are two primary ways to set this task up in SSIS. One is the Copy Database Wizard. This is an easy way for either a quick solution or someone that hasn't been in SSIS as much. In order to do this you need SSIS services installed and SQL Agent running. The other option is to create your own SSIS package while utilizing the "Transfer Database Task". This is almost as easy as the wizard in respect to the setup. There is no scripting required. I'm sure more than a few DBA's just got happy on that note. 

Below you can see both methods and how to set them up quickly.

Copy Database Wizard

1) Connect to the instance using SSMS. Right click the database you want to copy. Scroll to Tasks and select "Copy Database..."

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//copydb_1.gif" alt="" title="" />
</div>

2) First screen as the others are self-explaining. Enter your source server and either sql or windows authentication. Next screen enter your destination instance

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//copydb_2.gif" alt="" title="" />
</div>

3) The transfer method is the critical aspect of the task. If you are able to have downtime on the database then the simple detach and attach method is quick and all you contend with is network latency. Without downtime options the SMO task is very good. Performance is a factor on the primary database but connectivity will still be available. I typically use SMO due to uptime standards. Make your selections and click next

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//copydb_3.gif" alt="" title="" />
</div>

4) Next select the database to copy and click next. For DR of course never tick Move operations

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//copydb_4.gif" alt="" title="" />
</div>

5) Configuring the copied database is probably where you need to put the most thought in. Of course stay true to your disk configuration and alter and default locations as needed. Name the new database something meaningful. In my case I show this as DBA05_DR. We want a recovery database that may contain any configuration changes that were made over the time frame so tick the Drop any database on the destination... option.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//copydb_5.gif" alt="" title="" />
</div>

6) Next configuration screen is your choice. I again use meaningful naming conventions. I also utilize the windows event log for these tasks so I and other systems personal only have to go one place for troubleshooting. 

7) The next screen will give you scheduling options. Remember that even if you do not schedule this wizard and hence package creation, the job is still created on your instance. So if this is a onetime situation make sure you go back and clean up the job created

8) Click Finish on the last screen and that's all it takes. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//copydb_6.gif" alt="" title="" />
</div>

Second option is to create the job in SSIS yourself. This can be useful if you have 3 or 4 databases you want to copy or run scripts before or after. It's a good idea to run DBCC CHECKDB after the transfer and other tasks that will ensure the copy was handled on the instance correctly. Primary concern is to make sure you have a working copy of the database in case of an emergency. You don't want to find out at the time of a failure if your copied database is corrupted. 

1) Open BIDS and create a new Integrated Services Project
  
2) Rename the Package1 to something meaningful
  
3) Drag over the "Transfer Database Task" to the Control Flow section
  
4) Double click the task so you open the Editor.
  
5) Name it something meaningful again
  
6) Click "Databases" in the left panel
  
7) Fill in all the options basically as you did in the wizard we just went through.
  
8) In the Source Database section is the key options to mimic the SMO and Copy method. By default you will probably see DatabaseOffline. In order to use SMO change this to DatabaseOnline. This will gray out the last options sense attach is not the method any longer.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//copydb_7.gif" alt="" title="" />
</div>

Save your package and upload it to your SSIS instance. Schedule a job off the SSIS package you just created and again, we're done. Good luck and please, always test your DR systems with a simulated LIVE disaster scenario. Don't want to find out they don't work when you really need them 🙂