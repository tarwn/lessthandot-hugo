---
title: How to move datafiles to a new drive in SQL Server
author: SQLDenis
type: post
date: 2010-12-17T15:18:32+00:00
ID: 978
excerpt: |
  For one of the databases that I have to manage we were running out of space, so we got a shiny new 10.9 TB sized drive.  
  
  I was asked to move some files used by one database to this new drive. I decided to write up a little blog post just in case you&hellip;
url: /index.php/datamgmt/dbprogramming/how-to-move-datafiles-to-a-new-drive-in/
views:
  - 58104
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server Admin
tags:
  - administration
  - files
  - sql server 2005
  - sql server 2008

---
For one of the databases that I have to manage we were running out of space, so we got a shiny new 10.9 TB sized drive.

<img src="/wp-content/uploads/blogs/DataMgmt/Drive.PNG" alt="" title="" width="359" height="506" />

I was asked to move some files used by one database to this new drive. I decided to write up a little blog post just in case you ever need to do this so that you don't backup and restore (with move) because there is another way.

First create this test database with 3 data files and 1 log file, the data files will be in the C:DB_Files directory

sql
USE master
GO

CREATE DATABASE [TestMove] ON  PRIMARY 
( NAME = N'TestMove', FILENAME = N'C:DB_FilesTestMove.mdf' , SIZE = 2048KB , FILEGROWTH = 1024KB ), 
( NAME = N'TestMove2', FILENAME = N'C:DB_FilesTestMove2.ndf' , SIZE = 2048KB , FILEGROWTH = 1024KB ), 
( NAME = N'TestMove3', FILENAME = N'C:DB_FilesTestMove3.ndf' , SIZE = 2048KB , FILEGROWTH = 1024KB )
 LOG ON 
( NAME = N'TestMove_log', 
FILENAME = N'C:MSSQLDATATestMove_log.ldf' ,
 SIZE = 1024KB , FILEGROWTH = 10%)
GO
```

Now just create a test table, insert one row and do a simple select.

sql
USE TestMove
GO

CREATE TABLE Test(id INT)
GO
INSERT Test VALUES(1)
GO

SELECT * FROM Test
```

Now instead of having the data files in the C:DB\_Files we want to move them to the D:DB\_Files directory. You can use ALTER DATABASE...MODIFY FILE to do that, you just specify the new file locations, just make sure that the directory exists.

The following code will change the location of the data files

sql
USE master;
GO
ALTER DATABASE TestMove
MODIFY FILE
(
    NAME = TestMove,
    FILENAME = N'D:DB_FilesTestMove.mdf'
);
GO

USE master;
GO
ALTER DATABASE TestMove
MODIFY FILE
(
    NAME = TestMove2,
    FILENAME = N'D:DB_FilesTestMove2.ndf'
);
GO

USE master;
GO
ALTER DATABASE TestMove
MODIFY FILE
(
    NAME = TestMove3,
    FILENAME = N'D:DB_FilesTestMove3.ndf'
);
GO
```

You will see the following message

> The file “TestMove” has been modified in the system catalog. The new path will be used the next time the database is started.
  
> The file “TestMove2” has been modified in the system catalog. The new path will be used the next time the database is started.
  
> The file “TestMove3” has been modified in the system catalog. The new path will be used the next time the database is started.

**[EDIT]**
  
Paul Randal mentioned that you don't have to shut down SQL Server, you can also just take the database OFFLINE, see here for more detail: http://www.sqlmag.com/blogs/SQLServerQuestionsAnswered/SQLServerQuestionsAnswered/tabid/1977/entryid/72328/A-Safe-Method-for-Moving-a-Database-to-a-New-Location.aspx
  
**[/EDIT]**

Now, the first thing you want to do is stop SQL Server or take the database offline. You can stop SQL Server in a variety of ways, if you want to use the command line (NET STOP), take a look here: [Using the Command Line to manage SQL Server services][1]. You can also use the SQL Server Configuration manager, services under Control Panel/Administrative Tools or you can use SSMS.

To take the database offline, you can run this

sql
ALTER DATABASE TestMove
SET OFFLINE;
```

If you do not stop SQL Server or take the database offline, you won't be able to move the files and you will get a message like the one below
  
<img src="/wp-content/uploads/blogs/DataMgmt/cannotmove.PNG" alt="" title="" width="479" height="303" />
  


After SQL Server is stopped, move the files to the new location

In my case, here is where the files are currently C:DB_Files
  
<img src="/wp-content/uploads/blogs/DataMgmt/beforeMove2.PNG" alt="" title="" width="613" height="451" />

And after the move, this is the location of the files now D:DB_Files
  

  
<img src="/wp-content/uploads/blogs/DataMgmt/afterMove2.PNG" alt="" title="" width="607" height="445" />

Start SQL Server again or make the database online again.

To set the database online, run this

sql
ALTER DATABASE TestMove
SET ONLINE;
```

After SQL Server is up and running. run the following query.

sql
SELECT name, physical_name AS CurrentLocation, state_desc
FROM sys.master_files
WHERE database_id = DB_ID(N'TestMove');
```
<div class="tables">
  <p>
    You should see something like the following
  </p>
  
  <table>
    <tr>
      <th>
        name
      </th>
      
      <th>
        CurrentLocation
      </th>
      
      <th>
        state_desc
      </th>
    </tr>
    
    <tr>
      <td>
        TestMove
      </td>
      
      <td>
        D:DB_FilesTestMove.mdf
      </td>
      
      <td>
        ONLINE
      </td>
    </tr>
    
    <tr>
      <td>
        TestMove_log
      </td>
      
      <td>
        C:MSSQLDATATestMove_log.ldf
      </td>
      
      <td>
        ONLINE
      </td>
    </tr>
    
    <tr>
      <td>
        TestMove2
      </td>
      
      <td>
        D:DB_FilesTestMove2.ndf
      </td>
      
      <td>
        ONLINE
      </td>
    </tr>
    
    <tr>
      <td>
        TestMove3
      </td>
      
      <td>
        D:DB_FilesTestMove3.ndf
      </td>
      
      <td>
        ONLINE
      </td>
    </tr>
    
    <table>
    </table>
  </table>
</div>

Finally, just run this simple query again to verify that you didn't corrupt anything

sql
USE TestMove
GO

SELECT * FROM Test
```

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://wiki.ltd.local/index.php/Using_the_Command_Line_to_manage_SQL_Server_services
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22