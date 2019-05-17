---
title: SQLCop behind a Proxy
author: Ted Krueger (onpnt)
type: post
date: 2010-10-18T21:09:42+00:00
ID: 925
excerpt: 'SQLCop uses an internet connection to ensure that all checks and updates that are added are maintained.  When SQLCop first loads, it checks the SQLCopConfig.xml file to determine if a new SQLCop.xml file needs to be downloaded.  If SQLCop is unable to download the configuration file, it will automatically use the previously saved sqlcop.xml file.  Because of this, a remote file request is always performed when you start up SQLCop.In order to work around this problem with proxy servers there is one of two things you can do to run SQLCop.'
url: /index.php/datamgmt/datadesign/sqlcop-and-proxy/
views:
  - 6131
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - best practices
  - sql server 2008
  - sql server community
  - sqlcop

---
Today my good friend Aaron Lowe ([Blog][1] | [Twitter][2]) let me know he was having problems getting [SQLCop][3] to run. The error would come up as soon as the executable would be run to do the initial install and run of the program. Proxy servers lock down internet traffic to the point explicit allowances must be set in order for programs like SQLCop to function.

SQLCop uses an internet connection to ensure that all checks and updates that are added are maintained. When SQLCop first loads, it checks the SQLCopConfig.xml file to determine if a new SQLCop.xml file needs to be downloaded. If SQLCop is unable to download the configuration file, it will automatically use the previously saved sqlcop.xml file. Because of this, a remote file request is always performed when you start up SQLCop.

In order to work around this problem with proxy servers there is one of two things you can do to run SQLCop.

  1. Ask the network administrator to add the changes to the proxy
  2. Download two XML files from LessThanDot that are required by SQLCop

> The two files are very lightweight! So don't be scared. They really won't bite.

The first is the **[SQLCop.xml][4]**. This file holds all of the checks and links to resources that SQLCop uses. 

The second file is the **[SQLCopConfig.xml][5]**. This file holds the version levels of SQLCop which is important to know where the version and update levels SQLCop are at.

Place these two files in the same directory as the SQLCop.exe and SQLCop will run without requiring the check for any updates or pull down new updates to these files.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/sqlcop_programfiles.gif" alt="" title="" width="353" height="167" />
</div>

We appreciate everyone's feedback on SQLCop and any problems or suggestions that you have are always welcome. You can post [forum topics on LessThanDot][6] to let us know. Also, SQLCop and LessThanDot is run and maintained from [donations][7]. If you can help, we greatly appreciate it. 🙂

 [1]: http://www.aaronlowe.net/
 [2]: http://twitter.com/Vendoran
 [3]: http://sqlcop.lessthandot.com/
 [4]: http://sqlcop.lessthandot.com/sqlcop.xml
 [5]: http://sqlcop.lessthandot.com/sqlcopconfig.xml
 [6]: http://forum.lessthandot.com/viewforum.php?f=145
 [7]: http://lessthandot.com/donate.php