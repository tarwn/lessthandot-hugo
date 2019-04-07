---
title: Getting the biggest tables in your SQL Server database
author: SQLDenis
type: post
date: 2013-02-08T08:07:00+00:00
ID: 1977
excerpt: Sometimes you want to quickly see what the biggest tables are in your database. Maybe someone just gave you a brand new database and you want to see which tables take up the most space. I usually use the sp_spaceused stored procedure, it runs quickly and gives me the data that I need. Just be aware of these remarks
url: /index.php/datamgmt/dbprogramming/getting-the-biggest-tables-in/
views:
  - 25035
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - sqlcop
  - tables

---
Sometimes you want to quickly see what the biggest tables are in your database. Maybe someone just gave you a brand new database and you want to see which tables take up the most space. I usually use the sp_spaceused stored procedure, it runs quickly and gives me the data that I need. Just be aware of these remarks

> When you drop or rebuild large indexes, or drop or truncate large tables, the Database Engine defers the actual page deallocations, and their associated locks, until after the transaction commits. Deferred drop operations do not release allocated space immediately. Therefore, the values returned by sp_spaceused immediately after dropping or truncating a large object may not reflect the actual disk space available. 

You could use updateusage but I don&#8217;t bother, I just want to get a rough size

Here is the query that will give you the 10 biggest tables in your SQL Server database

sql
CREATE TABLE #temp (name varchar(100), rows int,reserved varchar(100), data varchar(100),index_size  varchar(100),unused  varchar(100))

CREATE TABLE #loop (id int identity, name varchar(1000))
INSERT #loop
SELECT SCHEMA_NAME(schema_id) +'.' + name 
FROM sys.tables
WHERE type = 'U'


SET NOCOUNT ON
DECLARE @LoopId int
DECLARE @MaxID int
DECLARE @cmd varchar(1100)
DECLARE @TableName varchar(1000)


SELECT @LoopId= 1
SELECT @MaxID = max(id) from #loop

WHILE @LoopId <= @MaxID
BEGIN
	SELECT @cmd = 'insert #temp exec sp_spaceused '''
	SELECT @TableName = name from #loop where id = @LoopId
	SELECT @cmd = @cmd + @TableName + ''''

	EXEC( @cmd )


	SET @LoopId = @LoopId + 1
END


SELECT TOP 10 * 
FROM #temp
ORDER BY CONVERT(bigint,REPLACE(reserved,' KB','')) DESC

DROP TABLE #temp, #loop
```
This post is part of the informational section of SQLCop, this is why I wrote the code in such a way that you can still run it against a SQL Server 2000 instance.