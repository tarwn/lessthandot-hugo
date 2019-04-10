---
title: Using EXECUTE AS OWNER
author: Ted Krueger (onpnt)
type: post
date: 2012-04-19T14:35:00+00:00
ID: 1599
excerpt: 'It’s very common to come across user-defined modules that are written to execute as the owner.  Not to be confused with the EXECUTE AS, the EXECUTE AS Clause will change the account context that is used to validate if the procedure, function etc… can be&hellip;'
url: /index.php/datamgmt/dbadmin/using-execute-as-owner/
views:
  - 20116
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server

---
It’s very common to come across user-defined modules that are written to execute as the owner.  Not to be confused with the EXECUTE AS, the EXECUTE AS Clause will change the account context that is used to validate if the procedure, function etc… can be executed under the credentials.  This can be useful when you want to control permissions to a certain extent.  However, it can also be problematic when certain ownership changes have been made.

**Troubleshooting Failures** 

One of the most common failures that you will run into when the EXECUTE AS OWNER is used, is a failure to execute the module with access denied error messages.  Now, at first this can be confusing but the error does point to the area that is the culprit.  This error can even be shown when a user is in the sysadmnin server role and has complete control of the machine.  For example, a SQL Express installation may be used for mobile applications and later synchronized back to a repository.  Even with the sysadmin server role membership, a call to say, a procedure using the EXECUTE AS OWNER, can fail based on the ownership hierarchy that is in place.

**Database Ownership** 

First, review the ownership of a database.  If a database is created under sa ownership, the ownership flows to all the objects under it unless specifically changed.  This would allow the EXECUTE AS OWNER to be used freely and execute everything that falls in the confines of the database ownership and sa context.  If the owner is change, possibly during a restore or some other reasoning, the objects that fall under it will fail with the EXECUTE AS OWNER are essentially broken.

Let’s look at an example.  Create the following database

sql
CREATE DATABASE [OwnerTest] ON  PRIMARY 
( NAME = N'OwnerTest', FILENAME = N'C:OwnerTest.mdf' , SIZE = 409600KB , FILEGROWTH = 2%)
 LOG ON 
( NAME = N'OwnerTest_log', FILENAME = N'C:OwnerTest_log.ldf' , SIZE = 52224KB , FILEGROWTH = 2%)
GO
ALTER DATABASE [OwnerTest] SET RECOVERY SIMPLE 
GO
USE OwnerTest
GO
EXEC sp_changedbowner sa
GO
```

Notice we changed the owner to sa.  This was to set the database up for the example. The owner, by default, would be the account executing the statement above otherwise.

Create a simple procedure to test

sql
CREATE PROCEDURE Get_OwnerText
WITH EXECUTE AS OWNER
AS
SELECT SUSER_SNAME()
GO
```
 

Run the procedure and review the results

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-139.png?mtime=1334852510"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-139.png?mtime=1334852510" width="225" height="122" /></a>
</div>

This is all expected behavior.

Create another database on a different instance but leave the owner as a user that does not have rights on the first SQL Server instance to any objects in the database itself or a valid login.

sql
CREATE DATABASE [OwnerTest2] ON  PRIMARY 
( NAME = N'OwnerTest2', FILENAME = N'C:OwnerTest2.mdf' , SIZE = 409600KB , FILEGROWTH = 2%)
 LOG ON 
( NAME = N'OwnerTest2_log', FILENAME = N'C:OwnerTest2_log.ldf' , SIZE = 52224KB , FILEGROWTH = 2%)
GO
ALTER DATABASE [OwnerTest2] SET RECOVERY SIMPLE 
GO
```

Run a simple backup of the database created on the second instance

sql
BACKUP DATABASE [OwnerTest2] TO  DISK = N'C:OwnerTest2.bak' WITH NOFORMAT, NOINIT,  NAME = N'OwnerTest2-Full Database Backup', SKIP, NOREWIND, NOUNLOAD,  STATS = 10
GO
```

Results

58 percent processed.

77 percent processed.

81 percent processed.

92 percent processed.

Processed 216 pages for database 'OwnerTest2', file 'OwnerTest2' on file 1.

100 percent processed.

Processed 2 pages for database 'OwnerTest2', file 'OwnerTest2_log' on file 1.

BACKUP DATABASE successfully processed 218 pages in 0.227 seconds (7.472 MB/sec).

 

Use the backup from the second instance housing database OwnerTest2 and restore it, replacing OwnerTest on the first instance.

sql
USE Master
GO
RESTORE DATABASE [OwnerTest] FROM  DISK = N'C:OwnerTest2.bak' WITH  FILE = 1,  
MOVE N'OwnerTest2' TO N'C:OwnerTest.mdf',  
MOVE N'OwnerTest2_log' TO N'C:OwnerTest_log.ldf',  
NOUNLOAD,  REPLACE,  STATS = 10
GO
```


Results

58 percent processed.

100 percent processed.

Processed 216 pages for database 'OwnerTest', file 'OwnerTest2' on file 1.

Processed 2 pages for database 'OwnerTest', file 'OwnerTest2_log' on file 1.

RESTORE DATABASE successfully processed 218 pages in 0.547 seconds (3.101 MB/sec).

 

Make sure that a query window is open with the context as a user that is in the sysadmin server role or that is an owner of the database, OwnerTest.  This is simply to show the high level access that the account has, still will not execute the procedure successfully.  Execute the procedure that was created earlier, Get_OwnerTest.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-140.png?mtime=1334852510"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-140.png?mtime=1334852510" width="624" height="65" /></a>
</div>


<span class="MT_red"> 

<p>
  Msg 15517, Level 16, State 1, Procedure Get_OwnerText, Line 0
</p>

<p>
  Cannot execute as the database principal because the principal “dbo” does not exist, this type of principal cannot be impersonated, or you do not have permission.
</p>

<p>
  </span>
</p>

<p>
   
</p>

<p>
  The message comes up that directs us to the user that is executing the procedure does not have rights to do so.  This is due to the database ownership mentioned before.  At this point, the owner of the database is a login that does not exist on the server nor has any access or capable means of being authenticated.  Since this occurs, the procedure fails because the account is either in an orphaned state or simply, the account context does not have access.  The more interesting aspect of this is, the owner will show as the previous over of the database.  This essentially means a normal troubleshooting step of validating the owner may tell someone that the ownership is fine.  However, it is not. You can review this by using the SP_HELPDB system procedure.
</p>

<p>
  <div class="image_block">
    <a href="/wp-content/uploads/blogs/DataMgmt/-141.png?mtime=1334852511"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-141.png?mtime=1334852511" width="624" height="137" /></a>
  </div>
</p>

<p>
  Run the last change ownership procedure to move the ownership back to sa or another account that has full rights.
</p>

<p>
  <pre lang="tsql">EXEC sp_changedbowner sa
Go</pre>
</p>

<p>
   
</p>

<p>
  Run the procedure again to validate it is successful.
</p>

<p>
  <div class="image_block">
    <a href="/wp-content/uploads/blogs/DataMgmt/-142.png?mtime=1334852511"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-142.png?mtime=1334852511" width="318" height="146" /></a>
  </div>
</p>

<p>
  <strong>Conclusion</strong>
</p>

<p>
  Using the EXECUTE AS Clause does have value and can allow the ability for modules to be executed with limited needs put into security.  However, it isn’t as secure as creating a solid security setup and schema setup and controlling each object execution under the executing account. It is also prone to some errors when the chain of ownership has been broken.
</p>