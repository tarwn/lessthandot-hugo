---
title: Scripting data in SQL Server 2012
author: SQLDenis
type: post
date: 2012-01-02T20:35:00+00:00
ID: 1475
excerpt: |
  SQL Server 2008 has the Generate Scripts option inside the script wizard. In SQL Server 2012, this has changed and is kind of buried. Here is how to find it now
  
  Right click on the database select Tasks-->Generate Scripts-->Set Scripting Options-->Adv&hellip;
url: /index.php/datamgmt/datadesign/scripting-data-in-sql-server/
views:
  - 9651
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - scripting

---
SQL Server 2008 has the Script Data option inside the script wizard. 

You would right click on the database, select Tasks–>Generate Scripts–>Pick the DB
  
and then you would be presented with scripting options, one of the options would be to script data, this is located under Table/View options. See the image below of how it looks like

<div class="image_block">
  <a href="/wp-content/uploads/users/SQLDenis/ScriptWizard2008.png?mtime=1325606711"><img alt="" src="/wp-content/uploads/users/SQLDenis/ScriptWizard2008.png?mtime=1325606711" width="485" height="430" /></a>
</div>

In SQL Server 2012, this has changed and is kind of buried. Here is how to find it now.

Right click on the database, select Tasks–>Generate Scripts–>Set Scripting Options–>Advanced. Scroll down to Types of data to script

Now you can select Schema only, Data only, Schema and Data. See image below of what the options should look like

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/scriptData.PNG?mtime=1325543364"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/scriptData.PNG?mtime=1325543364" width="463" height="411" /></a>
</div>

Don't forget to install the [SSMS Toolspack][1], that will enable you to script just one table or just results from a query as well

 [1]: http://www.ssmstoolspack.com/