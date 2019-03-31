---
title: Getting the tables with the most rows from your database
author: SQLDenis
type: post
date: 2013-02-08T15:50:00+00:00
excerpt: 'Sometimes you want to quickly see what tables have the most rows in your database. This is especially rue if you inherited a new database and you want to know some stats about this database. Instead of doing a count(*) against every table, I usually just use the sp_spaceused stored procedure. this will run many times faster, usually it is instantaneous.'
url: /index.php/datamgmt/dbprogramming/getting-the-tables-with-the/
views:
  - 3000
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server Admin
tags:
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - sqlcop
  - tables

---
Sometimes you want to quickly see what tables have the most rows in your database. This is especially true if you inherited a new database and you want to know some stats about this database. Instead of doing a count(*) against every table, I usually just use the sp_spaceused stored procedure. this will run many times faster, usually it is instantaneous. 

Just be aware of these remarks from Books On Line

> When you drop or rebuild large indexes, or drop or truncate large tables, the Database Engine defers the actual page deallocations, and their associated locks, until after the transaction commits. Deferred drop operations do not release allocated space immediately. Therefore, the values returned by sp_spaceused immediately after dropping or truncating a large object may not reflect the actual disk space available. 

You could use updateusage but I don&#8217;t bother, I just want to get a rough count

Here is the query that will give you that

<pre>CREATE TABLE #temp (name varchar(100), rows int,reserved varchar(100), data varchar(100),index_size  varchar(100),unused  varchar(100))

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

WHILE @LoopId &lt;= @MaxID
BEGIN
	SELECT @cmd = 'insert #temp exec sp_spaceused '''
	SELECT @TableName = name from #loop where id = @LoopId
	SELECT @cmd = @cmd + @TableName + ''''

	EXEC( @cmd )


	SET @LoopId = @LoopId + 1
END


SELECT TOP 10 name, rows 
FROM #temp
ORDER BY rows DESC

DROP TABLE #temp, #loop</pre>

This post is part of the informational section of SQLCop. I have written the SQL in this way because I want SQLCop to be able to run this against a SQL Server 2000 instance