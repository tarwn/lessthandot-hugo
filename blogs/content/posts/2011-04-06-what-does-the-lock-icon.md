---
title: What does the lock icon mean in SSMS when looking at a stored procedure?
author: SQLDenis
type: post
date: 2011-04-06T12:37:00+00:00
excerpt: |
  SQL Server Management Studio will sometimes show a lock next to a stored procedure, there are several reasons why this might happen.
  
  The procedure is encrypted
  The procedure is a CLR stored procedure
  The user doesn't have the view definition permis&hellip;
url: /index.php/datamgmt/datadesign/what-does-the-lock-icon/
views:
  - 17927
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server Admin
tags:
  - permissions
  - ssms
  - stored procedures

---
SQL Server Management Studio will sometimes show a lock next to a stored procedure, there are several reasons why this might happen.

The procedure is encrypted
  
The procedure is a CLR stored procedure
  
The user doesn&#8217;t have the view definition permission for a stored proc

Let&#8217;s take a look at how this all works.

I will first create this database with two stored procedures, one is encrypted, the other one is not

<pre>CREATE DATABASE bla
GO

USE bla
GO

CREATE PROCEDURE prTest
AS
SELECT GETDATE()
GO

CREATE PROCEDURE prTestEncrypted WITH ENCRYPTION
AS
SELECT GETDATE()
GO</pre>

Now when I navigate to the Stored Procedures folder I see the following

<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/.evocache/Permissions.PNG/fit-400x320.PNG?mtime=1302099806" width="242" height="228" />

As you can see there is a lock on the encrypted stored procedure

Now, let&#8217;s create a new user with data reader and writer permissions

<pre>USE [master]
GO
CREATE LOGIN [test] WITH PASSWORD=N'test', DEFAULT_DATABASE=[bla], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO
USE [bla]
GO
CREATE USER [test] FOR LOGIN [test]
GO
USE [bla]
GO
EXEC sp_addrolemember N'db_datareader', N'test'
GO
USE [bla]
GO
EXEC sp_addrolemember N'db_datawriter', N'test'
GO</pre>

When I now login as that user I don&#8217;t see anything under the stored procedures folder.

<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/.evocache/Permissions2.PNG/fit-400x320.PNG?mtime=1302099815" width="269" height="173" />
  
  
In order to see the procedures, the user has to have execute permissions.
  
If I give execute permissions to the stored procedures for the user like this, the user should see them.

<pre>GRANT EXECUTE ON prTestEncrypted TO test
GRANT EXECUTE ON prTest TO test</pre>

The user will now see both procs with a lock on them

<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/.evocache/Permissions3.PNG/fit-400x320.PNG?mtime=1302099825" width="267" height="199" />

If I grant view definition to the user&#8230;.

<pre>GRANT VIEW DEFINITION ON prTestEncrypted TO test
GRANT VIEW DEFINITION ON prTest TO test</pre>

The user will now see the lock only on the encrypted stored procedures, just like if he/she was a db owner

<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/.evocache/Permissions.PNG/fit-400x320.PNG?mtime=1302099806" width="242" height="228" />

I think there should be different lock icons in SSMS, this way you will now if the proc is encrypted or that you don&#8217;t have view definition permissions&#8230;what do you think?

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22