---
title: Do you still use sp_change_users_login instead of ALTER USER UserName WITH LOGIN = UserName
author: SQLDenis
type: post
date: 2011-06-03T14:03:00+00:00
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

<pre>EXECUTE sp_change_users_login 'Update_One', 'UserName', 'UserName'</pre>

And here is the new way, much cleaner

<pre>ALTER USER UserName WITH LOGIN = UserName</pre>

Let&#8217;s write some code and see how this all works

First we are creating a new user named TestLogin

<pre>USE [master]
GO
CREATE LOGIN [TestLogin] WITH PASSWORD=N'test', DEFAULT_DATABASE=[master], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO</pre>

Now it is time to create a new database, in this database we will create the user TestLogin

<pre>CREATE DATABASE TestLogin
GO

USE TestLogin
GO


CREATE USER TestLogin FOR LOGIN TestLogin
GO</pre>

Now we will backup the database

<pre>USE master
GO

BACKUP DATABASE TestLogin TO  DISK = N'c:TempTestLogin.BAK' WITH NOFORMAT, INIT,  NAME = N'TestLogin-Full', 
SKIP, NOREWIND, NOUNLOAD,  STATS = 10</pre>

And now we can drop the database since we will create it again later anyhow

<pre>USE master
GO

DROP DATABASE TestLogin
GO</pre>

Since I want to show you the code so that you can run it on the same server, we will just drop and recreate the login

<pre>USE [master]
GO

IF  EXISTS (SELECT * FROM sys.server_principals WHERE name = N'TestLogin')
DROP LOGIN [TestLogin]
GO


USE [master]
GO
CREATE LOGIN [TestLogin] WITH PASSWORD=N'test', DEFAULT_DATABASE=[master], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO</pre>

Now, we will create the same database again with the same user

<pre>CREATE DATABASE TestLogin
GO

USE TestLogin
GO


CREATE USER TestLogin FOR LOGIN TestLogin
GO

USE master
GO</pre>

Go ahead and restore the backup we created before

<pre>ALTER DATABASE TestLogin SET SINGLE_USER WITH ROLLBACK IMMEDIATE
GO

RESTORE DATABASE TestLogin FROM  DISK = N'c:TempTestLogin.BAK' WITH  FILE = 1,
NOUNLOAD,  REPLACE,  STATS = 10
GO


ALTER DATABASE TestLogin SET MULTI_USER WITH ROLLBACK IMMEDIATE
GO</pre>

We now will use this database and use the sp\_change\_users_login procedure to see if any SIDs are mismatched

<pre>USE TestLogin
GO

EXECUTE sp_change_users_login 'report'</pre>

On my machine, I get the following back

<pre>UserName	UserSID
TestLogin	0x7ED6E205155E9C40BA684E72453BAE1B</pre>

We can easily test this because if you try to login as that user and then execute the command below you will get an error message

<pre>USE testlogin
GO</pre>

Msg 916, Level 14, State 1, Line 1
  
The server principal &#8220;TestLogin&#8221; is not able to access the database &#8220;TestLogin&#8221; under the current security context.

Leave that query window open for now, we will get back to it later.
  
Now let&#8217;s fix the user by mapping the user to the login

<pre>EXECUTE sp_change_users_login 'Update_One', 'TestLogin', 'TestLogin'</pre>

Now this query doesn&#8217;t return any rows since the user has been fixed

<pre>EXECUTE sp_change_users_login 'report'</pre>

Now refresh the query or connect again and run this

<pre>USE testlogin
GO</pre>

As you can see it is fine now

Disconnect from the DB with the TestLogin account and then drop the database

<pre>USE master
GO

DROP DATABASE TestLogin
GO</pre>

We will create the database again, create the user again and finally we will restore the database

<pre>USE master
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
GO</pre>

Just as before, we are getting back the mis matched SID

<pre>USE TestLogin
GO

EXECUTE sp_change_users_login 'report'</pre>

<pre>UserName	UserSID
TestLogin	0x7ED6E205155E9C40BA684E72453BAE1B</pre>

You will get the same error from before if you try to connect to this database

<pre>USE testlogin
GO</pre>

Msg 916, Level 14, State 1, Line 1
  
The server principal &#8220;TestLogin&#8221; is not able to access the database &#8220;TestLogin&#8221; under the current security context.

Here is how to do the same thing with ALTER USER as with sp\_change\_users_login 

<pre>ALTER USER TestLogin WITH LOGIN = TestLogin</pre>

As you can see, this doesn&#8217;t return anything anymore

<pre>EXECUTE sp_change_users_login 'report'</pre>

So, start using ALTER USER UserName WITH LOGIN = UserName instead of sp\_change\_users\_login to fix the mappings, sp\_change\_users\_login is on the <del>endangered</del> deprecated list and will be removed in a feature versions.