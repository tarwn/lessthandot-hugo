---
title: Where are the Data Sources and Data Source Views in SSIS 2012?
author: Axel Achten (axel8s)
type: post
date: 2012-03-19T12:23:00+00:00
ID: 1566
excerpt: "While I'm still preparing to teach a MS6235A SSIS training I use a SQL 2012 RTM environment to test the demo's. One of the demo's is to show how a Connection Manager is made based on a Data Source View. In BIDS 2008 the Data Sources and Data Source View&hellip;"
url: /index.php/datamgmt/ssis/where-are-the-data-sources/
views:
  - 10710
rp4wp_auto_linked:
  - 1
categories:
  - SSIS
tags:
  - bids
  - connection manager
  - data source
  - data source view
  - sql server data tools

---
While I'm still preparing to teach a [MS6235A SSIS training][1] I use a SQL 2012 RTM environment to test the demo's. One of the demo's is to show how a Connection Manager is made based on a Data Source View. In BIDS 2008 the Data Sources and Data Source Views are found in the Solution Explorer:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV1.png?mtime=1332152100"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV1.png?mtime=1332152100" width="165" height="157" /></a>
</div>

To create a new Data Source you right click on the Data Source folder and follow the wizard:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV2.png?mtime=1332152402"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV2.png?mtime=1332152402" width="280" height="177" /></a>
</div>

Next you would make a Data Source View based on the Data Source just created by right clicking on the Data Source View folder and again following the instructions:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV3.png?mtime=1332152786"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV3.png?mtime=1332152786" width="297" height="188" /></a>
</div>

In the screen that pop's up you get the list of available Data Sources and after selecting one the Connection Manager is created.
  
The last step is to create the Connection Manager that will be based on the Data Source. This is accomplished by right clicking in the Connection Manager pane and by selecting “New Connection From Data Source…”:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV4.png?mtime=1332153238"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV4.png?mtime=1332153238" width="694" height="363" /></a>
</div>

To use the Data Source View a “Data Flow Task” must be created and in the task an “OLE DB Source” is used. When configuring the OLD DB Source you can finally refer to the Data Source View:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV6.png?mtime=1332160446"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV6.png?mtime=1332160446" width="190" height="117" /></a>
</div>

Now when I try the same scenario with the new “SQL Server Data Tools” you can immediatly see that there are no Data Sources and Data Source Views in the solution explorer:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV7.png?mtime=1332165228"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV7.png?mtime=1332165228" width="207" height="174" /></a>
</div>

What you can see is a Connection Managers folder. Right clicking the folder gives me the option to create a new Connection Manager. In the wizard I connect to my AdventureWorks2012 database and when I complete the wizard my Solution Explorer shows the new Connection Manager:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV8.png?mtime=1332166288"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV8.png?mtime=1332166288" width="209" height="235" /></a>
</div>

But more important when you look at the Connection Managers pane in the package itself you'll see that there is a “(project)ConnectionManagerName” added to the package:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV9.png?mtime=1332166495"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DSV9.png?mtime=1332166495" width="241" height="58" /></a>
</div>

And this Connection Manager can be used from within an OLE DB Source control.
  
So with the new Data Tools for SQL Server 2012 it looks like we've lost the Data Source Views for SSIS projects. But creating Data Sources in the project scope stays possible altough it's now renamed to Connection Managers.

 [1]: http://www.microsoft.com/learning/en/us/course.aspx?ID=6235A&locale=en-us