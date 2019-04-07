---
title: SQLCop A Tool To Highlight Potential Problems With Your Database
author: SQLDenis
type: post
date: 2010-07-25T16:44:38+00:00
ID: 855
excerpt: 'I wrote a blog post 437 days ago asking you the reader if you would be interested in a FxCop tool for SQL Server. That post can be found here: SQLCop, FxCop For SQL Server, Would You Be Interested in This?. Today I am pleased to announce that the first&hellip;'
url: /index.php/datamgmt/dbprogramming/sqlcop-a-tool-to-highlight-potential-pro/
views:
  - 23313
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server Admin
tags:
  - sql
  - sql server
  - sqlcop

---
I wrote a blog post 437 days ago asking you the reader if you would be interested in a FxCop tool for SQL Server. That post can be found here: [SQLCop, FxCop For SQL Server, Would You Be Interested in This?][1]. Today I am pleased to announce that the first version of this tool is available. The tool is free and will remain free, we will never charge for it.

The tool is only 412 kb to download and no installer is needed. The reason we didn&#8217;t do an add in in SSMS is because we wanted people to be able to run it it by itself. The tool was tested on Windows XP, Vista and Windows 7 (64 and 32 bit)

A big thanks to [George Mastros][2] for coding this tool, you can also thank him on twitter here: http://twitter.com/gmmastros

The tool will not modify any of your database, it will only check and if it finds issues it will list the objects if possible. It will also show you a blog post or wiki article explaining why something is a problem and how to remedy it.

To run the tool you just download it from the download link at the SQLCop homepage here: http://sqlcop.ltd.local/index.php

After that you run the tool, you will get the following login screen.

![Login screen][3]

Enter the server name, database name and credentials (or windows authentication)

After that you will see a screen similar to the one on the bottom. From the left side tree menu expand the node that you are interested in. When you click on an item the right part of the screen will show an html page showing you why the issue might be problematic and how to remedy it. Just remember that these issues might not be issues for you, maybe some of these objects are not used anymore but people are scared to drop them because they might break things down the road. So use your best judgement!

<img src="http://sqlcop.ltd.local/screenshots/sqlcop4.png" alt="Issues" title="Issues" width="650" />

Here is a video of the tool in action so that you can see how it works before you install it
  
[video:youtube:MCIZDUu2kC4]

More info about this tool can be found here, including where to post questions if you have any problems: http://sqlcop.ltd.local/index.php

You can also leave a comment here but we prefer you do it in the [SQLCop help forum][4] because the format is better suited for questions and answers

 [1]: /index.php/DataMgmt/DataDesign/sqlcop-fxcop-for-sql-server-would-you-be
 [2]: /index.php/All/?disp=authdir&author=10
 [3]: http://sqlcop.ltd.local/screenshots/sqlcop1.png "Login screen"
 [4]: http://forum.ltd.local/viewforum.php?f=145