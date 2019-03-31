---
title: Discovering new things (for me) in SSMS
author: SQLDenis
type: post
date: 2013-02-10T12:42:00+00:00
excerpt: 'One of the good things about working in technology is that you always will discover new things. The other day I created a blog post: Listing all your SQL Server databases ordered by size. Buck Woody replied to me on twitter that this is also built into&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/discovering-new-things-for-me/
views:
  - 17764
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - sqlcop
  - ssms
  - tables

---
One of the good things about working in technology is that you always will discover new things. The other day I created a blog post: [Listing all your SQL Server databases ordered by size][1]. Buck Woody replied to me on twitter that this is also built into SSMS. He tweeted the following link: [Selecting Columns to Display in SSMS][2]

I decided to check it out. I knew already about the details pane, I use it all the time if I need to script more than one object. I blogged about it here as well: [Scripting all jobs on SQL Server 2005/2008 into one file][3]

What I did not know is that you can change the columns that are shown. Click on he databases folder in SSMS and press F7, this will bring up the details pane, it looks somewhat like the image below

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/DatabasesDeTails.PNG?mtime=1360507029"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/DatabasesDeTails.PNG?mtime=1360507029" width="926" height="343" /></a>
</div>

Right click on any of the columns and you will see the following menu

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/DatabasesDeTailsChoose.PNG?mtime=1360507043"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/DatabasesDeTailsChoose.PNG?mtime=1360507043" width="247" height="497" /></a>
</div>

Now I don&#8217;t care for some of the things that are shown by default, I unchecked some of them and checked others. Here is what it looks like now, much better for what I need to see at a glance

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/DatabasesDeTails2.PNG?mtime=1360507051"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/DatabasesDeTails2.PNG?mtime=1360507051" width="860" height="342" /></a>
</div>

You can sort the results by clicking on a column, click it again and it is sorted in the reversed order
  
Did you know that if you do CTRL + A, CTRL + C, it copies it as TAB delimited, just paste it into Excel

What about you, what &#8216;new&#8217; things have you discovered in SSMS?

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/listing-all-your-sql-server
 [2]: http://blogs.msdn.com/b/buckwoody/archive/2008/09/02/selecting-columns-to-display-in-ssms.aspx
 [3]: /index.php/DataMgmt/DBProgramming/scripting-all-jobs-on-sql-server-2005-20