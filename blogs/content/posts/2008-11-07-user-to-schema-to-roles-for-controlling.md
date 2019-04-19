---
title: User to Schema to Roles for controlling security
author: Ted Krueger (onpnt)
type: post
date: 2008-11-07T15:01:28+00:00
ID: 199
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/user-to-schema-to-roles-for-controlling/
views:
  - 6403
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
Recently a friend asked the question on the security side of things. Security in SQL Server can be confusing and I'm not going to try to convince you it can't be. Sure there are about 5 people out there that understand is completely and all aspects of the landscape but the majority need to research things like schemas and roles every other time they need to work on them.
  
The question went something along the lines of this.

User1 already exists in DB1 and owns schema controller1. The objective is to give User1 ability to create tables under another database (DB2) under a specific schema while retaining restrictive access to the rest of DB2. Wow, even calling something DB2 scares me.
  
So there are a few options here. One which most DBA's that give in will do is adding User1 to DB2 and just grant create table. Sorry but that isn't security. Option 2 would be to allow chaining. Another one I highly recommend not doing. Chaining defeats the reasoning of the security scope SQL Server allows you to have. It is hard to manage and even harder to control. My solution is schemas and roles. What we can do is seriously limit User1 in DB2 to only one schema and give him only the permissions we want them to have.
  
So let's get started. First we need our starting point. DB1, User1 associated to login User1 and schema controller1. Here's the script to create this

I'm doing everything in SQL Server 2008 but this will work in 2005 also

```sql
USE master
Go
CREATE DATABASE [db1] ON PRIMARY 
( NAME = N'db1', FILENAME = N'C:db1.mdf' , SIZE = 3072KB , MAXSIZE = 200MB, FILEGROWTH = 1024KB )
LOG ON 
( NAME = N'db1_log', FILENAME = N'C:db1_log.ldf' , SIZE = 1024KB , MAXSIZE = 2048MB , FILEGROWTH = 100MB)
GO
CREATE LOGIN [User1] WITH PASSWORD=N'password', DEFAULT_DATABASE=[master], 
  DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
GO
USE [db1]
GO
CREATE USER [User1] FOR LOGIN [User1] WITH DEFAULT_SCHEMA=[dbo]
GO
CREATE SCHEMA [controller1] AUTHORIZATION [User1]
GO
```
Now that we have the starting point we have something to work with. Here is the point that became misleading when this question was originally asked. Schemas are only a bowl where you can group objects. That's really it. They are only a form in which we have the power to organize our databases so they are easier managed and controlled. If you are a seasoned DBA or aspiring DBA then this probably has you excited. I know it did for me because organized databases are only benefits to us as DBAs for recovery and quick manageability.
  
Schemas are limited to that though. Permissions are only controlled when schemas are combined with things like roles. So given the task we have you would think you could simply say we could create another schema in DB2 and then associate User1 with the schema while granting create table. There's more we need though to get there. Roles fill the void!

In saying that let's create our second database DB2

```sql
USE master
Go
CREATE DATABASE [db2] ON PRIMARY 
( NAME = N'db2', FILENAME = N'C:db2.mdf' , SIZE = 3072KB , MAXSIZE = 200MB, FILEGROWTH = 1024KB )
LOG ON 
( NAME = N'db2_log', FILENAME = N'C:db2_log.ldf' , SIZE = 1024KB , MAXSIZE = 2048MB , FILEGROWTH = 100MB)
GO
```
We now need to create our role, schema and user in DB2. The key here is to tie it all together. To get it out of the way create the database user under the login User1. 

```sql
USE [db2]
GO
CREATE USER [User1] FOR LOGIN [User1] WITH DEFAULT_SCHEMA=[dbo]
GO
```
Next we need to create our role. We don't do anything with security yet other than create the role. Advanced security options come later and out of scope here. Our entire objective here is to control what User1 does in DB2.

```sql
CREATE ROLE [Controller2_Role] AUTHORIZATION [dbo]
GO
```
Now we need to tie it all together. Create the schema. Instead of authorizing the database user to the schema we authorize the role to the schema. This is the key that is overlooked often in the landscape.

```sql
CREATE SCHEMA [controller2] AUTHORIZATION [Controller2_Role]
GO

Exec sp_addrolemember 'Controller2_ROle','User1'
```
Notice we just added User1 as a member to our newly created role. We now have a landscape in which we can control User1 in DB2. At this point User1 still cannot do anything but given a simple GRANT CREATE TABLE to User1 will show us the results of our work
  
Let's try it out. Execute the GRANT statement as a sysadmin. Disconnect from the instance and reconnect under the sql authenticated user of User1 under the DB1 context.
  
Execute this create table statement from DB1 to create a table in DB2

```sql
CREATE TABLE db2.Controller2.tbl1 (col1 int)
```
Now to test security by trying this statement

```sql
CREATE TABLE db2.dbo.tbl1 (col1 int)
```
The first create table statement worked and created a table under the schema Controller2. However as we tested out that was the limit and User1 was not able to create a table under dbo schema.
  
So we seemed to have reached the objective with a solution of schemas, roles and database users. All databases will benefit from schemas and roles you create by making them manageable and more secure. A database can be designed and called perfection but if security is not handled in a controlled environment it will fail. Trust me!