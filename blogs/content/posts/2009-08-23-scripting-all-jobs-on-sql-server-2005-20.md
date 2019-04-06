---
title: Scripting all jobs on SQL Server 2005/2008 into one file
author: SQLDenis
type: post
date: 2009-08-23T10:49:00+00:00
excerpt: 'In the Scripting All The Jobs On Your SQL Server Instance Into Separate Files By Using SMO post I showed you how you can use SMO to script all the jobs into separate files. But what if you want to have them all in one file? You can of course use SMO for&hellip;'
url: /index.php/datamgmt/dbprogramming/scripting-all-jobs-on-sql-server-2005-20/
views:
  - 357204
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server

---
In the [Scripting All The Jobs On Your SQL Server Instance Into Separate Files By Using SMO][1] post I showed you how you can use SMO to script all the jobs into separate files. But what if you want to have them all in one file? You can of course use SMO for that or you can do it from SSMS. Most people don&#8217;t know about this and that is the reason for this little post.

You know that you can not select more that one job if you expand the job folder, but you can if you are in detail view

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut4.png?mtime=1357605366"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut4.png?mtime=1357605366" width="279" height="280" /></a>
</div>

So navigate to the job folder, press on it and hit the F7 key or from the toolbar: view&#8211;>Object Explorer Details

That will bring up a view like the following

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut5.png?mtime=1357605388"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/SCriptOut5.png?mtime=1357605388" width="616" height="389" /></a>
</div>

Now you can select the jobs that you want, right click&#8211;>Script Job as&#8211;>CREATE to&#8211;>File and you specify the location and that is all.

Hopefully this will be handy for you.



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DBAdmin/scripting-all-the-jobs-on-your-sql-serve
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22