---
title: 'SQL Server Integration Services tools to have – Expression Editor & Tester'
author: Ted Krueger (onpnt)
type: post
date: 2011-12-08T10:46:00+00:00
ID: 1416
excerpt: 'Developing SSIS is only as restricting and time consuming as the development studio in which we are forced to develop in.  With SQL Server 2012, Visual Studio 2010 shell has been adopted.  This has been a long winded complaint from the community - havin&hellip;'
url: /index.php/datamgmt/datadesign/sql-server-integration-services-tools/
views:
  - 4606
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - SSIS

---
Developing SSIS is only as restricting and time consuming as the development studio in which we are forced to develop in.  With SQL Server 2012, Visual Studio 2010 shell has been adopted.  This has been a long winded complaint from the community &#8211; having to install BIDS 2008 and also Visual Studio 2010 on most development machines.  Well, that isn’t a complaint any longer.  I never had much of a problem with the two installs, side-by-side.  In fact, in most cases, as a consultant, I have to have both full versions of 2008 and 2010 installed anyhow.  Let’s not go there though.  The problem has been solved thanks to the SQL Server Product Teams and we now have one ruling development platform.

Aside from BIDS, SQL Server Data Tools and the like, there are a few tools that I would find it hard to live without.  These tools add value to maintaining SSIS packages, rapid development and performance tuning.  They are: [Expression Editor & Tester][1], [BIDS Helper][2]<span style="text-decoration: underline;">,</span> and one I found more recently, [SSIS Configuration File Editor][3].

**Expression Editor & Tester**

Expressions in SSIS are not as easy as writing C# or VB.NET, in my opinion .  I find that is common among most professionals developing anything in SSIS or SSRS.  These two share the same pain of expressions being used widely throughout them in order to achieve true dynamic mobility.

Expression Editor & Tester allows us to develop and test expressions outside of BIDS or SQL Server Data Tools.  That alone is a major efficiency gain in not having to slow down machines while having an integrated development environment open and running while writing expressions and ensuring they work properly.  In most cases, design phases of our SSIS pinpoint areas that will require expressions to be used.  This may be for connections or simply a derived table expression that is required.

The [Expression Editor & Tester][1] is a standalone executable that can be placed anywhere in your file system.  It must be accompanied by the ExpressionEditor.dll.  To make the task easier of making this tool quickly accessible, create an All Programs listing for it.  To do this, create a shortcut to the ExpressionTester.exe and place it in the Programs folder.  For example, this would be on my machine

C:UserstkruegerAppDataRoamingMicrosoftWindowsStart MenuProgramsExpressionTester.lnk

 

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-65.png?mtime=1323055267"><img src="/wp-content/uploads/blogs/DataMgmt/-65.png?mtime=1323055267" alt="" width="165" height="105" /></a>
</div>

 

Expression Editor has all the same system variables and functions that are listed in the editor within BIDS and Data Tools.  For example, let’s take a look at a common need; adding date and time to a backup file name.  Now using the editor, build the expression using the same names for the variables to locate the backup share, database and server name.

 

<div class="image_block">
  <a href="/media/blogs/DataMgmt/-66.png?mtime=1323055268"><img src="/wp-content/uploads/blogs/DataMgmt/-66.png?mtime=1323055268" alt="" width="624" height="460" /></a>
</div>

 

In this case the expression was written (wrong at first, I might add) and Evaluated.  This was done without requiring BIDS or anything else open.  It is a very thin executable and extremely useful and valuable to being more efficient in getting your expressions written quickly.

The documentation on Codeplex on this tool is quite good and goes into depth on the tool.  The example I used is similar to the one used there.  This is mostly due to this task being so common I suspect.  It was the first and best expression I came up with to show the tool in use.

Tomorrow we will go over BIDS Helper which is arguably the most valuable tool that is on Codeplex for SSIS.

 [1]: http://expressioneditor.codeplex.com/
 [2]: http://bidshelper.codeplex.com/
 [3]: http://ssisconfigeditor.codeplex.com/