---
title: Giving users the ability to change a stored procedure without making them db_owner
author: SQLDenis
type: post
date: 2013-01-08T15:07:00+00:00
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

<pre>GRANT ALTER  ON OBJECT::ProcName TO UserName
GRANT ALTER  ON  ProcName TO UserName</pre>

Let&#8217;s take a look at this by writing some code. First create a new database

<pre>CREATE DATABASE Test
GO</pre>

Create a user in that database

<pre>USE [master]
GO
CREATE LOGIN [TestUser] WITH PASSWORD=N'Test', DEFAULT_DATABASE=[test], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO
USE [test]
GO
CREATE USER [TestUser] FOR LOGIN [TestUser]
GO</pre>

Now create this very simple stored procedure

<pre>CREATE PROC prTest
AS
SELECT GETDATE()</pre>

Open up another connection, login as TestUser with the password Test. If you try to execuce the stored procedure you will get an error

<pre>EXEC prTest</pre>

Msg 229, Level 14, State 5, Procedure prTest, Line 1
  
The EXECUTE permission was denied on the object &#8216;prTest&#8217;, database &#8216;test&#8217;, schema &#8216;dbo&#8217;.

In the window where you have all the permissions, give execute permissions to this stored procedure to the TestUser. You can use GRANT EXECUTE ON ProcName TO UserName to accomplish this, there is no need to give additional privileges like db_owner or sysadmin

<pre>GRANT EXECUTE  ON prTest  TO TestUser</pre>

Now you will see that TestUser can execute the stored procedure.

If TestUser wants to modify the stored procedure you can give TestUser ALTER StoredProc permissions. First let&#8217;s see what happens if TestUser tries to modify the stored procedure before we gave the permissions

<pre>ALTER PROC prTest
AS
SELECT GETDATE()</pre>

And here is the error

Msg 3701, Level 14, State 20, Procedure prTest, Line 3
  
Cannot alter the procedure &#8216;prTest&#8217;, because it does not exist or you do not have permission.

Execute the following in the window where you have all the permissions

<pre>GRANT ALTER ON prTest TO TestUser</pre>

Now run this again in the window where you are logged in as TestUser

<pre>ALTER PROC prTest
AS
SELECT GETDATE()</pre>

As you now saw, this succeeded.

You can also take away privileges, execute the following in the window where you have all the permissions.

<pre>REVOKE ALTER ON prTest  TO TestUser</pre>

Now when you try to alter the stored procedure again it will fail. 

Here is the other way to give permissions, I like the other syntax better, it is shorter and there are no double colon characters

<pre>GRANT ALTER  ON OBJECT::prTest TO TestUser</pre>

Now trying to modify the stored procedure will work again.

There you have it, it is simple to give users access to modify only the stored procedures that you want, no need to give elevated privileges that might bite you in the butt down the road