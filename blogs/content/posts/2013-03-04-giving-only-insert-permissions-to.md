---
title: Giving only insert permissions to a table for a new login
author: SQLDenis
type: post
date: 2013-03-04T13:00:00+00:00
ID: 2021
excerpt: |
  There was a requirement to create a new user who would have only insert permissions to one table, this user would also have insert and select permissions to another table.
  
  This is pretty simple to accomplish. First create this simple database with tw&hellip;
url: /index.php/datamgmt/dbprogramming/giving-only-insert-permissions-to/
views:
  - 32455
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - security
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - tables

---
There was [a requirement][1] to create a new user who would have only insert permissions to one table, this user would also have insert and select permissions to another table.

This is pretty simple to accomplish. First create this simple database with two tables

```sql
CREATE DATABASE TestPermission
GO

USE TestPermissions
GO


CREATE TABLE  TestAccess(id INT)
INSERT TestAccess VALUES (1)
GO

CREATE TABLE  TestAccess2(id INT)
INSERT TestAccess2 VALUES (1)
GO
```

Create the new user

```sql
USE [master]
GO
CREATE LOGIN [SomeTestUser] WITH PASSWORD=N'TestPAss', DEFAULT_DATABASE=[master], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO
USE [TestPermissions]
GO
CREATE USER [SomeTestUser] FOR LOGIN [SomeTestUser]
GO
```

Now just give the user insert permissions to the TestAccess table

```sql
USE TestPermissions
GO


GRANT INSERT ON TestAccess TO SomeTestUser
```

Login as the newly created user and try to run the following

```sql
SELECT * FROM TestAccess
```

Here is the error message that you will get

_Msg 229, Level 14, State 5, Line 1
  
The SELECT permission was denied on the object 'TestAccess', database 'TestPermissions', schema 'dbo'._

Running the insert statement is no problem

```sql
INSERT TestAccess VALUES (1)
```

Go back to the admin connection and run the following

```sql
GRANT SELECT,INSERT ON TestAccess2 TO SomeTestUser
```

As you can see you can combine privileges with the GRANT statement, you don't have to do the separately

You just gave insert and select permissions to SomeTestUser for the TestAccess2 table

Now if you go back to the connection, you can run the following without a problem

```sql
INSERT TestAccess2 VALUES (1)
SELECT * FROM TestAccess2
```

In general you probably want a user to have read permissions or write permissions for all the tables, in that case you can use a role. The following will give read and write permissions for all the tables in the database

```sql
USE [TestPermissions]
GO
EXEC sp_addrolemember N'db_datareader', N'SomeTestUser'
GO
EXEC sp_addrolemember N'db_datawriter', N'SomeTestUser'
GO
```


 [1]: http://stackoverflow.com/questions/15204118/how-do-i-create-a-user-in-sql-server-that-only-has-access-to-one-table-and-can