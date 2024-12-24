---
title: SQL Server DBA Tip 16 – Working with Files and Folders
author: Ted Krueger (onpnt)
type: post
date: 2011-05-17T10:08:00+00:00
ID: 1179
excerpt: "Today's tip is a short but powerful one.  On many occasions a DBA is forced to deal with files in some way.  It may be the need to download and move a file (or folder) around in order to prepare for importing the contents into a database or simply maint&hellip;"
url: /index.php/datamgmt/dbprogramming/sql-server-dba-tip-filesystemhelper-sqlclr/
views:
  - 12988
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Today's tip is a short but powerful one.  On many occasions a DBA is forced to deal with files in some way.  It may be the need to download and move a file (or folder) around in order to prepare for importing the contents into a database or simply maintain the contents or auditing levels of them.  

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-58.png?mtime=1305633960"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/-58.png?mtime=1305633960" width="840" height="188" /></a>
</div>

Working with files through SQL Server is more difficult than your everyday select of data from a database.  SQL Server is made to stay within the walls it has built.  This means we have to use other methods in order to accomplish what we need to do with file and folders. 

Jason Strate ([Blog][1] | [Twitter][2]) had an idea of putting together one method that I have also used over the years – [SQLCLR File System Helper][3].  SQLCLR provides a path for a DBA and Developer to external resources that once was extremely hard to access from within a database.  With SQLCLR we've been given the power of writing .NET code and placing and executing it directly within our databases.  Now this can be dangerous given the nature of the resource utilization when it comes to .NET.  However, done with planning and thought beforehand, using SQLCLR can be extremely valuable.

  Jason and I have put together a set of procedures and functions that are most common in respect to file and folder operations. 

  **Utility.DirectoryCreate**

  **Utility.DirectoryDelete**

  **Utility.DirectoryDeleteContents**

  **Utility.DirectoryList**

  **Utility.DirectoryMoveTo**

  **Utility.DirectorySize**

  **Utility.FileCopy**

  **Utility.FileCreate**

  **Utility.FileDelete**

  **Utility.FileMove**

  **Utility.FileRename**

  **Utility.FileSearch**

  **Utility.FileSearchInternal**

The set of procedures and functions that are available cover a wide range of needs when you are working with files and folders.  Some of these procedures are used daily on my own scheduled tasks and very beneficial in making those schedules tasks run smooth and clean. 

Take a look at your own SQL Server tasks that are running either on a schedule or manually and see how these methods can help you in making them more efficient. 

As always with Codeplex items, leave us feedback on the project so Jason and I can continue on making it better and more robust.

 [1]: http://www.jasonstrate.com/
 [2]: http://twitter.com/#!/stratesql
 [3]: http://filesystemhelper.codeplex.com/