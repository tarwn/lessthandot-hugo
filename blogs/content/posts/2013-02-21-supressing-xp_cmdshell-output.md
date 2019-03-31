---
title: Suppressing xp_cmdshell output
author: SQLDenis
type: post
date: 2013-02-21T09:13:00+00:00
excerpt: 'Starting with SQL Server 2005 xp_cmdshell is turned off by default as a security measure. You have to explicitly enable xp_cmdshell it. If you have to move files around on a SQL Server box there are probably better ways than using xp_cmdshell, you can use SSIS,&hellip;'
url: /index.php/datamgmt/dbprogramming/supressing-xp_cmdshell-output/
views:
  - 31347
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - features
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms
  - xp_cmdshell

---
Starting with SQL Server 2005 xp_cmdshell is turned off by default as a security measure. You have to explicitly [enable xp_cmdshell][1] it. If you have to move files around on a SQL Server box there are probably better ways than using xp\_cmdshell, you can use SSIS, powershell, plain old DOS. But old habits are difficult to break and maybe you have ton of legacy code. Someone asked how to supress output from xp\_cmdshell when moving a file

I you move a file with xp_cmdshell, you will get the following output
  
output
  
1 file(s) moved.
  
NULL

If I want to move a file from c:temp to c:tempold, I would execute the following

<pre>xp_cmdshell 'move c:tempbla.txtc:tempoldbla.txt'</pre>

Here is what it looks like in SSMS

[<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/xp_cmdshellMove.PNG?mtime=1361444713" width="432" height="224" />][2]

As you can see you get a resultset, in order to surpress that, you can add no\_output to the xp\_cmdshell call, no_output is an optional parameter, specifying that no output should be returned to the client.

Here is what the command looks like

<pre>xp_cmdshell 'move c:tempbla.txt c:tempoldbla.txt',no_output</pre>

Executing the command like that will not return a resultset anymore

 [1]: /index.php/DataMgmt/DataDesign/how-to-enable-xp_cmdshell-on-sql-server-2005
 [2]: /wp-content/uploads/blogs/DataMgmt/Denis/xp_cmdshellMove.PNG?mtime=1361444713