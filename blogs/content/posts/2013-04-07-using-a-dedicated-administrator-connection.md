---
title: Using a Dedicated Administrator Connection via SSMS
author: SQLDenis
type: post
date: 2013-04-07T13:55:00+00:00
ID: 2061
excerpt: |
  If you need to use a Dedicated Administrator Connection (DAC) via SSMS, you can't just use the Connect Object Explorer, if you try you will get the following error
  
  Dedicated administrator connections are not supported via SSMS as it establishes multi&hellip;
url: /index.php/datamgmt/dbprogramming/using-a-dedicated-administrator-connection/
views:
  - 48985
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dac
  - how to
  - sql server 2008
  - sql server 2012

---
If you need to use a Dedicated Administrator Connection (DAC) via SSMS, you can't just use the Connect Object Explorer, if you try you will get the following error

Dedicated administrator connections are not supported via SSMS as it establishes multiple connections by design. (Microsoft.SqlServer.Management.SqlStudio.Explorer)

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Dac.PNG?mtime=1365341742"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Dac.PNG?mtime=1365341742" width="619" height="174" /></a>
</div>

So what do you do? You can connect from within SSMS, you need to click on the `Database Engine Query` icon

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Dac2.PNG?mtime=1365342094"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/Dac2.PNG?mtime=1365342094" width="416" height="87" /></a>
</div>

Prefix the servername with Admin: Instead of **(local)**, you would do **Admin:(local)**

Another way to connect is by using sqlcmd with the command-line option A
  
Here is an example with a username and password

sqlcmd -S ServerName -U sa -P Password –A

You can also use a trusted connection

sqlcmd -S (local) -E -A

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/dac3.PNG?mtime=1365343123"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/dac3.PNG?mtime=1365343123" width="671" height="142" /></a>
</div>

If you want to know more about DAC, check out [Using a Dedicated Administrator Connection][1]

 [1]: http://msdn.microsoft.com/en-us/library/ms189595(v=sql.110).aspx