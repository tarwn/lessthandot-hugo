---
title: Why do you need additional privileges for truncate table compared to delete?
author: SQLDenis
type: post
date: 2012-10-07T12:04:00+00:00
excerpt: One of the people on my team wants to have the ability to truncate tables on the staging database while this person is testing, why are special permissions required compared to a delete?
url: /index.php/datamgmt/datadesign/why-do-you-need-additional/
views:
  - 30773
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - delete
  - how to
  - permissions
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - truncate table

---
One of the people on my team wants to have the ability to truncate tables on the staging database while this person is testing. Here is what Books On Line has about permissions for the TRUNCATED statement

> The minimum permission required is ALTER on table\_name. TRUNCATE TABLE permissions default to the table owner, members of the sysadmin fixed server role, and the db\_owner and db_ddladmin fixed database roles, and are not transferable.

Before I answer why someone would need ALTER TABLE permissions when the person already has DELETE permissions, let&#8217;s run some code that will show the &#8216;problem&#8217;.

First create a Test database, add one table and insert one row

<pre>CREATE DATABASE Test
go

USE Test
GO

CREATE TABLE TestTruncate(Id int)
GO

INSERT TestTruncate values(1)
GO</pre>

Now create a new user and give the user datareader and datawriter permissions

<pre>USE master
GO
CREATE LOGIN TestLogin WITH PASSWORD=N'Test', 
DEFAULT_DATABASE=master, CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO
USE Test
GO
CREATE USER TestLogin FOR LOGIN TestLogin
GO
USE Test
GO
ALTER ROLE db_datareader ADD MEMBER TestLogin
GO
USE Test
GO
ALTER ROLE db_datawriter ADD MEMBER TestLogin
GO</pre>

Now that the user is created, login as that user and run the TRUNCATE TABLE command

<pre>TRUNCATE TABLE TestTruncate</pre>

_Msg 1088, Level 16, State 7, Line 1
  
Cannot find the object &#8220;TestTruncate&#8221; because it does not exist or you do not have permissions._

As you can see, you don&#8217;t have permission. A delete will work just fine

<pre>DELETE TestTruncate</pre>

(1 row(s) affected)

Before I give you a workaround, let&#8217;s try to figure out why the minimum requirement is ALTER TABLE. What is the difference between a DELETE and a TRUNCATE in terms of logging? When a TRUNCATE occurs, the operation does not log individual row deletions, a DELETE operation does. The reason this is important is because if you have a trigger on the table, in needs to be disabled before the TRUNCATE occurs. **Now you know why ALTER TABLE is required, triggers need to be disabled.**

<pre>ALTER TABLE SomeTable DISABLE TRIGGER SomeTrigger</pre>

And in order to disable the trigger, ALTER TABLE permissions are required as a minimum.

But I don&#8217;t want people altering tables on our staging and QA servers, so here is one way of giving the person the ability to TRUNCATE a table without giving them permissions explicitly. Create a stored procedure and use WITH EXECUTE AS, this will define the execution context of the stored procedure. In the example below, I picked a user that has sufficient privileges to perform the TRUNCATE.

<pre>CREATE PROCEDURE prTruncate
WITH EXECUTE AS 'SuperUser'
AS
TRUNCATE TABLE TestTruncate
GO</pre>

All you have to do is give your user execute permissions to the stored procedure you just created

<pre>GRANT EXECUTE ON prTruncate TO TestLogin
GO</pre>

Now if you execute the stored procedure as the TestLogin user, you will see it will run just fine

<pre>EXEC prTruncate</pre>

Hope this helps someone in the future who is filling up his log these days