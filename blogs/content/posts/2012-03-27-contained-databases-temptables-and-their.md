---
title: Contained Databases, Temporary Tables and Collations
author: Axel Achten (axel8s)
type: post
date: 2012-03-27T11:54:00+00:00
ID: 1579
excerpt: "In SQL Server 2012 we now can have Contained Databases. To be precise we now can have partially Contained Databases. The complete definition is found in the MSDN Database. In short a Contained Database holds it's configuration and security information s&hellip;"
url: /index.php/datamgmt/dbprogramming/mssqlserver/contained-databases-temptables-and-their/
views:
  - 11075
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - collation
  - contained database
  - tempdb
  - temporary table

---
In SQL Server 2012 we now can have Contained Databases. To be precise we now can have partially Contained Databases. The complete definition is found in the [MSDN Database][1]. In short a Contained Database holds it's configuration and security information so you should be able to move these databases easily between different SQL Servers without having to create users or other configuration items. But as we all know Collations can cause serious problems, certainly if there is a difference between the Collation of the database itself and tempdb. So what happens if we create a Contained Database with a different Collation on a server? And does a Contained Database uses tempdb for it's Temporary Tables? Let's find out what happens with a normal database and after that wath happens with a Contained Databases.
  
First things first, check the Collation settings of the server and tempdb:

```sql
SELECT SERVERPROPERTY('collation'), DATABASEPROPERTYEX('tempdb','collation')
GO
```

In my case the server en tempdb Collation are the same, Case-Insensitive and Accent-Sensitive:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ContDB1.png?mtime=1332856134"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ContDB1.png?mtime=1332856134" width="394" height="64" /></a>
</div>

To start I create a database with a different Collation than the server default, in my case Case-Sensitive:

```sql
CREATE DATABASE NotContainedDB
	COLLATE SQL_Latin1_General_CP1_CS_AS
GO
```

From within the new database I create a regular and a Temporary Table and insert a value in it:

```sql
USE NotContainedDB
GO

CREATE TABLE NotContaintedData (
	NCDID int IDENTITY (1,1),
	NCDValue varchar (25)
	)
GO

INSERT INTO NotContaintedData
	VALUES ('CapitalData')

CREATE TABLE #NotContaintedTempData (
	NCTDID int IDENTITY (1,1),
	NCTDValue varchar (25)
	)
GO

INSERT INTO #NotContaintedTempData
	VALUES ('CapitalData')
GO
```

Now if we just write a little query to compare the values in both tables:

```sql
SELECT * FROM 
	NotContaintedData NCD
	INNER JOIN #NotContaintedTempData NCTD
	ON NCD.NCDValue = NCTD.NCTDValue
GO
```

We see that SQL Server is unable to compare the Case-Sensitive and Case-Insensitive data:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ContDB2.png?mtime=1332856142"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ContDB2.png?mtime=1332856142" width="996" height="64" /></a>
</div>

This behaviour is expected. So will it be the same with a Contained Database? To be able to test it we first have to enable the usage of Contained Databases:

```sql
SP_CONFIGURE 'contained database authentication', 1
GO
RECONFIGURE WITH OVERRIDE
GO
```

Now we can create a Contained Database, note that we can only use PARTIAL and the database is created with a different Collation than the server default:

```sql
CREATE DATABASE ContainedDB
	CONTAINMENT = PARTIAL
	COLLATE SQL_Latin1_General_CP1_CS_AS
GO
```

Now I create simular tables to the ones in the noncontained database:

```sql
USE ContainedDB
GO

CREATE TABLE ContaintedData (
	CDID int IDENTITY (1,1),
	CDValue varchar (25)
	)
GO

INSERT INTO ContaintedData
	VALUES ('CapitalData')

CREATE TABLE #ContaintedTempData (
	CTDID int IDENTITY (1,1),
	CTDValue varchar (25)
	)
GO

INSERT INTO #ContaintedTempData
	VALUES ('CapitalData')
GO
```

If we now run the query where both values are compared:

```sql
SELECT * FROM 
	ContaintedData CD
	INNER JOIN #ContaintedTempData CTD
	ON CD.CDValue = CTD.CTDValue
GO
```

We get a resultset:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ContDB3.png?mtime=1332856155"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ContDB3.png?mtime=1332856155" width="274" height="64" /></a>
</div>

So what does this means? Is the Temporary Table created in the Contained Database itself or is it stored in tempdb with the Collation of the Contained Database? When you execute the following query in both databases you'll find the answer:

```sql
SP_HELP #ContaintedTempData
GO
```

The query only works when executed from tempdb. So the Temporary Table is created in tempdb and in the results of the query you can see that the table is created with the Collation of the Contained Database:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ContDB4.png?mtime=1332856165"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/Axel8s/ContDB4.png?mtime=1332856165" width="832" height="277" /></a>
</div>

So we can conclude that Temporary Tables in a Contained Database are still created in tempdb. But in contrast to the behaviour of a regular database the Temporary Table of a Contained Database keep the Collation of the Contained Database.

 [1]: http://msdn.microsoft.com/en-us/library/ff929071(v=sql.110).aspx