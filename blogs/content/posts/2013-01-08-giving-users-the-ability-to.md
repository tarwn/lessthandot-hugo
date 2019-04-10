---
title: Giving users the ability to change a stored procedure without making them db_owner
author: SQLDenis
type: post
date: 2013-01-08T15:07:00+00:00
ID: 1906
excerpt: 'I once did some work for a company and noticed that they were running as sysadmin. When I asked why, their answer was that the stored procedures would not work otherwise. This is very bad practice, in general I create a user, and then give execute permi&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/giving-users-the-ability-to/
views:
  - 33132
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dcl
  - permissions
  - security
  - sql server 2008
  - sql server 2012
  - stored procedures

---
I once did some work for a company and noticed that they were running as sysadmin. When I asked why, their answer was that the stored procedures would not work otherwise. This is very bad practice, in general I create a user, and then give execute permissions to the procedures. In some instances the user might also get reader and writer permissions.

Yesterday I got a request from a person who wanted to be able to change two stored procedures on the staging server. There is no need to make this person a db_owner, you can just give the ALTER proc permissions. There are two flavors of the syntax

sql
GRANT ALTER  ON OBJECT::ProcName TO UserName
GRANT ALTER  ON  ProcName TO UserName
```

Let's take a look at this by writing some code. First create a new database

sql
CREATE DATABASE Test
GO
```

Create a user in that database

sql
USE [master]
GO
CREATE LOGIN [TestUser] WITH PASSWORD=N'Test', DEFAULT_DATABASE=[test], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO
USE [test]
GO
CREATE USER [TestUser] FOR LOGIN [TestUser]
GO
```

Now create this very simple stored procedure

sql
CREATE PROC prTest
AS
SELECT GETDATE()
```

Open up another connection, login as TestUser with the password Test. If you try to execuce the stored procedure you will get an error

sql
EXEC prTest
```

Msg 229, Level 14, State 5, Procedure prTest, Line 1
  
The EXECUTE permission was denied on the object 'prTest', database 'test', schema 'dbo'.

In the window where you have all the permissions, give execute permissions to this stored procedure to the TestUser. You can use GRANT EXECUTE ON ProcName TO UserName to accomplish this, there is no need to give additional privileges like db_owner or sysadmin

sql
GRANT EXECUTE  ON prTest  TO TestUser
```

Now you will see that TestUser can execute the stored procedure.

If TestUser wants to modify the stored procedure you can give TestUser ALTER StoredProc permissions. First let's see what happens if TestUser tries to modify the stored procedure before we gave the permissions

sql
ALTER PROC prTest
AS
SELECT GETDATE()
```

And here is the error

Msg 3701, Level 14, State 20, Procedure prTest, Line 3
  
Cannot alter the procedure 'prTest', because it does not exist or you do not have permission.

Execute the following in the window where you have all the permissions

sql
GRANT ALTER ON prTest TO TestUser
```

Now run this again in the window where you are logged in as TestUser

sql
ALTER PROC prTest
AS
SELECT GETDATE()
```

As you now saw, this succeeded.

You can also take away privileges, execute the following in the window where you have all the permissions.

sql
REVOKE ALTER ON prTest  TO TestUser
```

Now when you try to alter the stored procedure again it will fail. 

Here is the other way to give permissions, I like the other syntax better, it is shorter and there are no double colon characters

sql
GRANT ALTER  ON OBJECT::prTest TO TestUser
```

Now trying to modify the stored procedure will work again.

There you have it, it is simple to give users access to modify only the stored procedures that you want, no need to give elevated privileges that might bite you in the butt down the road