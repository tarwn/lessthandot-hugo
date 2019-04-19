---
title: How to give permissions to User-Defined Table Types
author: SQLDenis
type: post
date: 2012-03-21T17:26:00+00:00
ID: 1570
excerpt: How to give permissions to User-Defined Table Types. The syntax is not straight-forward.........
url: /index.php/datamgmt/datadesign/how-to-give-permissions-to/
views:
  - 31239
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
This came up a couple of days ago, a person created a User-Defined Table Type and then used the regular db\_datawriter/db\_datareader account to use this User-Defined Table Type and he would get the following error

**Msg 229, Level 14, State 5, Line 9
  
The EXECUTE permission was denied on the object 'SysObjectsCount', database 'testTVP', schema 'dbo'.**

Let's take a look at how to give permissions because if you do the following

sql
GRANT EXECUTE ON SysObjectsCount TO TestLogin
```

You will get this error

**Msg 15151, Level 16, State 1, Line 1
  
Cannot find the object 'SysObjectsCount', because it does not exist or you do not have permission.**

Let's fix this, I will show you some code so that you can reproduce this

First create the following login

sql
USE [master]
GO
CREATE LOGIN [TestLogin] WITH PASSWORD=N'test', DEFAULT_DATABASE=[master], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO
```

Now create the following database

sql
CREATE DATABASE testTVP
GO
```

Create a new user in the database that we just created and give the user db\_datareader and db\_datawriter roles

sql
USE testTVP
GO


CREATE USER TestLogin FOR LOGIN TestLogin
GO

EXEC sp_addrolemember N'db_datareader', N'TestLogin'
GO
USE [testTVP]
GO
EXEC sp_addrolemember N'db_datawriter', N'TestLogin'
GO
```
Now create the following type

sql
CREATE TYPE SysObjectsCount AS TABLE(quantity INT, xtype CHAR(2))
GO
```

Now run the following piece of code

sql
DECLARE @mySystableCount AS SysObjectsCount
 
INSERT @mySystableCount
SELECT COUNT(*),xtype FROM sysobjects
GROUP BY xtype
 
SELECT * FROM @mySystableCount
```

That runs fine right? Open another connection but this time login as TestLogin

Try to run that code again

sql
DECLARE @mySystableCount AS SysObjectsCount
 
INSERT @mySystableCount
SELECT COUNT(*),xtype FROM sysobjects
GROUP BY xtype
 
SELECT * FROM @mySystableCount
```
You get the following error

**Msg 229, Level 14, State 5, Line 9
  
The EXECUTE permission was denied on the object 'SysObjectsCount', database 'testTVP', schema 'dbo'.**

In order to give the permissions to testLogin, you need to execute the following code, yes the **::** is correct.

sql
GRANT EXECUTE ON TYPE::SysObjectsCount to TestLogin
```

That reminds me of how you would use fn_helpcollations()

sql
SELECT * FROM ::fn_helpcollations()
```

Just keep in mind that you need to have those double colons there. It would be nicer if you could just do something like this instead

_GRANT EXECUTE ON TYPE SysObjectsCount to TestLogin_

Anyway, execute this

sql
GRANT EXECUTE ON TYPE::SysObjectsCount to TestLogin
```

Now you should be able to run this code connected as TestLogin

sql
DECLARE @mySystableCount AS SysObjectsCount
 
INSERT @mySystableCount
SELECT COUNT(*),xtype FROM sysobjects
GROUP BY xtype
 
SELECT * FROM @mySystableCount
```

And if you look now in SSMS, you will be able to see the User-Defined Table Type in the User-Defined Table Types folder

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/tvp.PNG?mtime=1332357761"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/tvp.PNG?mtime=1332357761" width="305" height="481" /></a>
</div>

That's it for this post...hopefully this will help someone else in the future

See also my post [SQL Advent 2011 Day 18: Table-valued Parameters][1] for some more info about User-Defined Table Types

 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/sql-advent-2011-day-18