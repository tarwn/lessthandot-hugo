---
title: Location of files for new database in SQL Server
author: SQLDenis
type: post
date: 2011-11-17T22:51:00+00:00
ID: 1387
excerpt: |
  The following question was asked
  
  I've got a sql server that's set up to have database files to default to e drive, but the model's files are on c. will new database pick up the setting from the server and put them on e, or will they pick it up from t&hellip;
url: /index.php/datamgmt/datadesign/location-of-files-for-new/
views:
  - 3934
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - data files
  - files
  - log files
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
The following [question][1] was asked

> I've got a sql server that's set up to have database files to default to e drive, but the model's files are on c. will new database pick up the setting from the server and put them on e, or will they pick it up from the model and put them on c?

What SQL Server does is that it will pick up the settings from the server settings. We can easily verify that by running some scripts

To check where model has its path, you can run the following query

```sql
select * from master..sysaltfiles
where db_name(dbid) ='model'
go
```

On my laptop, the location for the files is as follows
  
C:Program FilesMicrosoft SQL ServerMSSQL11.MSSQLSERVERMSSQLDATAmodel.mdf
  
C:Program FilesMicrosoft SQL ServerMSSQL11.MSSQLSERVERMSSQLDATAmodellog.ldf

If I now create a new database like this

```sql
create database TestMeNow
GO
```

And if I now check for the location

```sql
select * from master..sysaltfiles
where db_name(dbid) ='TestMeNow'
go
```

I can see that it is the same as for model

C:Program FilesMicrosoft SQL ServerMSSQL11.MSSQLSERVERMSSQLDATATestMeNow.mdf
  
C:Program FilesMicrosoft SQL ServerMSSQL11.MSSQLSERVERMSSQLDATATestMeNow_log.ldf

Now, let's change it at the server level, this code below will make the default for data files on D:Data and log files on D:Log

```sql
USE [master]
GO
EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'SoftwareMicrosoftMSSQLServerMSSQLServer', N'DefaultData', REG_SZ, N'D:Data'
GO
EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'SoftwareMicrosoftMSSQLServerMSSQLServer', N'DefaultLog', REG_SZ, N'D:Log'
GO
```

Or you can do it by right clicking on the server –> properties –> Database Settings if you are not as comfortable running SQL. Here is what it looks like
  
[<img src="http://farm7.static.flickr.com/6043/6355955641_8d0daa2731_b.jpg" width="704" height="632" alt="DBSettings" />][2]

Now restart the SQL Server instance for the changes to take effect
  
Create a new database

```sql
create database TestMeNow3
GO
```

Now if you check, you will see that it placed the files in the location we have specified in the server settings

```sql
select * from master..sysaltfiles
where db_name(dbid) ='TestMeNow3'
go
```

Here is where the files are located
  
D:DataTestMeNow3.mdf
  
D:LogTestMeNow3_log.ldf

 [1]: http://forum.lessthandot.com/viewtopic.php?f=22&t=15783
 [2]: http://www.flickr.com/photos/denisgobo/6355955641/ "DBSettings "