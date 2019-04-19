---
title: Do you still use sp_change_users_login instead of ALTER USER UserName WITH LOGIN = UserName
author: SQLDenis
type: post
date: 2011-06-03T14:03:00+00:00
ID: 1201
excerpt: |
  If you are on SQL Server 2005 and up and you are still using sp_change_users_login (I know old habits die slow) then listen up, there is an easier way to fix permissions.
  
  If you have a login on one server and you have the same login on another server&hellip;
url: /index.php/datamgmt/datadesign/do-you-still-use-sp_change_users_login/
views:
  - 61956
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - permissions
  - sql server 2005
  - sql server 2008
  - sql server 2008 r2

---
If you are on SQL Server 2005 and up and you are still using sp\_change\_users_login (I know old habits die slow) then listen up, there is an easier way to fix permissions.

If you have a login on one server and you have the same login on another server and you did not use the sp\_help\_revlogin to create the login with the same SID then you have 2 options. drop and create the user(and apply all the permissions) or you can map the existing database user to a SQL Server login.

This is the old way

```sql
EXECUTE sp_change_users_login 'Update_One', 'UserName', 'UserName'
```

And here is the new way, much cleaner

```sql
ALTER USER UserName WITH LOGIN = UserName
```

Let's write some code and see how this all works

First we are creating a new user named TestLogin

```sql
USE [master]
GO
CREATE LOGIN [TestLogin] WITH PASSWORD=N'test', DEFAULT_DATABASE=[master], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO
```
Now it is time to create a new database, in this database we will create the user TestLogin

```sql
CREATE DATABASE TestLogin
GO

USE TestLogin
GO


CREATE USER TestLogin FOR LOGIN TestLogin
GO
```

Now we will backup the database

```sql
USE master
GO

BACKUP DATABASE TestLogin TO  DISK = N'c:TempTestLogin.BAK' WITH NOFORMAT, INIT,  NAME = N'TestLogin-Full', 
SKIP, NOREWIND, NOUNLOAD,  STATS = 10
```
And now we can drop the database since we will create it again later anyhow

```sql
USE master
GO

DROP DATABASE TestLogin
GO
```

Since I want to show you the code so that you can run it on the same server, we will just drop and recreate the login

```sql
USE [master]
GO

IF  EXISTS (SELECT * FROM sys.server_principals WHERE name = N'TestLogin')
DROP LOGIN [TestLogin]
GO


USE [master]
GO
CREATE LOGIN [TestLogin] WITH PASSWORD=N'test', DEFAULT_DATABASE=[master], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO
```

Now, we will create the same database again with the same user

```sql
CREATE DATABASE TestLogin
GO

USE TestLogin
GO


CREATE USER TestLogin FOR LOGIN TestLogin
GO

USE master
GO
```
Go ahead and restore the backup we created before

```sql
ALTER DATABASE TestLogin SET SINGLE_USER WITH ROLLBACK IMMEDIATE
GO

RESTORE DATABASE TestLogin FROM  DISK = N'c:TempTestLogin.BAK' WITH  FILE = 1,
NOUNLOAD,  REPLACE,  STATS = 10
GO


ALTER DATABASE TestLogin SET MULTI_USER WITH ROLLBACK IMMEDIATE
GO
```

We now will use this database and use the sp\_change\_users_login procedure to see if any SIDs are mismatched

```sql
USE TestLogin
GO

EXECUTE sp_change_users_login 'report'
```

On my machine, I get the following back

<pre>UserName	UserSID
TestLogin	0x7ED6E205155E9C40BA684E72453BAE1B</pre>

We can easily test this because if you try to login as that user and then execute the command below you will get an error message

```sql
USE testlogin
GO
```

Msg 916, Level 14, State 1, Line 1
  
The server principal “TestLogin” is not able to access the database “TestLogin” under the current security context.

Leave that query window open for now, we will get back to it later.
  
Now let's fix the user by mapping the user to the login

```sql
EXECUTE sp_change_users_login 'Update_One', 'TestLogin', 'TestLogin'
```

Now this query doesn't return any rows since the user has been fixed

```sql
EXECUTE sp_change_users_login 'report'
```

Now refresh the query or connect again and run this

```sql
USE testlogin
GO
```
As you can see it is fine now

Disconnect from the DB with the TestLogin account and then drop the database

```sql
USE master
GO

DROP DATABASE TestLogin
GO
```

We will create the database again, create the user again and finally we will restore the database

```sql
USE master
GO

CREATE DATABASE TestLogin
GO

USE TestLogin
GO


CREATE USER TestLogin FOR LOGIN TestLogin
GO

USE master
GO

ALTER DATABASE TestLogin SET SINGLE_USER WITH ROLLBACK IMMEDIATE
GO

RESTORE DATABASE TestLogin FROM  DISK = N'c:TempTestLogin.BAK' WITH  FILE = 1,
NOUNLOAD,  REPLACE,  STATS = 10
GO



ALTER DATABASE TestLogin SET MULTI_USER WITH ROLLBACK IMMEDIATE
GO
```

Just as before, we are getting back the mis matched SID

```sql
USE TestLogin
GO

EXECUTE sp_change_users_login 'report'
```

<pre>UserName	UserSID
TestLogin	0x7ED6E205155E9C40BA684E72453BAE1B</pre>

You will get the same error from before if you try to connect to this database

```sql
USE testlogin
GO
```

Msg 916, Level 14, State 1, Line 1
  
The server principal “TestLogin” is not able to access the database “TestLogin” under the current security context.

Here is how to do the same thing with ALTER USER as with sp\_change\_users_login 

```sql
ALTER USER TestLogin WITH LOGIN = TestLogin
```

As you can see, this doesn't return anything anymore

```sql
EXECUTE sp_change_users_login 'report'
```

So, start using ALTER USER UserName WITH LOGIN = UserName instead of sp\_change\_users\_login to fix the mappings, sp\_change\_users\_login is on the <del>endangered</del> deprecated list and will be removed in a feature versions.