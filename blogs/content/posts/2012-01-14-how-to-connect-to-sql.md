---
title: How to connect to SQL Server when your default database is unavailable
author: SQLDenis
type: post
date: 2012-01-14T18:11:00+00:00
ID: 1495
excerpt: "I was restoring a TB+ sized database on our staging database today. Someone needed to use a different database but he couldn't login because the database I was restoring was the default database for the login he was using. I told him to click on the Opt&hellip;"
url: /index.php/datamgmt/datadesign/how-to-connect-to-sql/
views:
  - 10452
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - connecting
  - connection
  - how to
  - sql server 2005
  - sql server 2008
  - tip

---
I was restoring a TB+ sized database on our staging database today. Someone needed to use a different database but he couldn't login because the database I was restoring was the default database for the login he was using. I told him to click on the Options button in the connection dialog and specify another database. I guess there was an misunderstanding because he couldn't get it to work. This means it is time for a blog post.

First, let's create two databases

```sql
CREATE DATABASE Test1
GO

CREATE DATABASE Test2
GO
```

Now create a new login named TestLogin with a password of Test

```sql
USE [master]
GO
CREATE LOGIN [TestLogin] WITH PASSWORD=N'Test', DEFAULT_DATABASE=[Test1]
```
Add the login we just created to the Test1 database and make the login part of the db_owner role

```sql
USE [Test1]
GO
CREATE USER [TestLogin] FOR LOGIN [TestLogin]
GO
USE [Test1]
GO
ALTER ROLE [db_owner] ADD MEMBER [TestLogin]
GO
```

Add the login we just created to the Test2 database and make the login part of the db_owner role

```sql
USE [Test2]
GO
CREATE USER [TestLogin] FOR LOGIN [TestLogin]
GO
USE [Test2]
GO
ALTER ROLE [db_owner] ADD MEMBER [TestLogin]
GO
```

Make sure that you can login with the TestLogin account

Now that you know that you can login with the TestLogin account, use another account and put the Test1 in offline mode

```sql
ALTER DATABASE Test1 SET OFFLINE
```

Now if you try to login with the TestLogin account, you will see the following error

_**Login failed for user 'TestLogin'. (Microsoft SQL Server, Error: 18456)**_

Here is what you need to do, on the connect to server window, click on the options button

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Options.PNG?mtime=1326571296"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Options.PNG?mtime=1326571296" width="405" height="204" /></a>
</div>

That will open the Connection Properties tab

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/BrowseServer.PNG?mtime=1326571269"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/BrowseServer.PNG?mtime=1326571269" width="401" height="348" /></a>
</div>

Whatever you do, do not select _Browse server..._ from the connect to database option, if you do that you will get the following error

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/BrowseServerError.PNG?mtime=1326571280"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/BrowseServerError.PNG?mtime=1326571280" width="614" height="167" /></a>
</div>

Just type the database name of the database that is not offline instead 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Test2.PNG?mtime=1326571594"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Test2.PNG?mtime=1326571594" width="388" height="174" /></a>
</div>

As you will see you can connect without a problem now

Hopefully this will help someone else in the future also