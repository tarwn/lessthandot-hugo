---
title: List all stored procedures that run at startup in SQL Server
author: SQLDenis
type: post
date: 2011-11-15T15:21:00+00:00
ID: 1383
excerpt: 'You know that you can mark a proc to run at startup in SQL Server. What you have to do is create the stored procedure in the master database and after that you have to set the startup flag to true. If your stored procedure name is spMyProc, the code wou&hellip;'
url: /index.php/datamgmt/datadesign/list-all-stored-procedures-that/
views:
  - 14776
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - administration
  - sql server 2005
  - sql server 2008

---
You know that you can mark a proc to run at startup in SQL Server. What you have to do is create the stored procedure in the master database and after that you have to set the startup flag to true. If your stored procedure name is spMyProc, the code would look like this.

sql
exec sp_procoption N'spMyProc', 'startup', 'on'
```

What if you want to list all the stored procedures on your server which are set to run on startup? Here is how you do this

sql
SELECT name,create_date,modify_date
FROM sys.procedures
WHERE OBJECTPROPERTY(OBJECT_ID, 'ExecIsStartup') = 1
```

That query will give you the name, creation date and modification date of every stored procedure that is set to run on startup.

Aaron Bertrand pointed out that you can also use the is\_auto\_executed column, here is what the query would look like now, much easier to read

sql
SELECT name,create_date,modify_date
FROM sys.procedures
WHERE is_auto_executed = 1
```

Creating stored procedures to run on startup is a nice way of ensuring that certain flags/tables/users are created/updated/deleted when the SQL instance is restarted, this way you won't forget to run that code that you have tucked away in some folder that needs to be run to initialize some data on startup.