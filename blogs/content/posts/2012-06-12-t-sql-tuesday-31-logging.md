---
title: 'T-SQL Tuesday #31 – Logging Simple Things'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-06-12T12:14:00+00:00
ID: 1647
excerpt: "How big are your core databases right now? Do you know how they got that way? Is that normal? These questions are impossible to answer just by looking at the database options dialog in SSMS. They are also questions I've had to try to answer in several d&hellip;"
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/t-sql-tuesday-31-logging/
views:
  - 9265
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - sql server
  - t-sql

---
 <img src="http://sqlblog.com/files/folders/30073/download.aspx" style="float: right; margin: 8px" alt="T-SQL Tuesday" />How big are your core databases right now? Do you know how they got that way? Is that normal? These questions are impossible to answer just by looking at the database options dialog in SSMS. They are also questions I've had to try to answer in several different environments, because without logging we not only didn't know what normal growth looked like for our system, we didn't know what tables and indexes were driving that growth. 

The answer lies in a very simple query and a little logging.

_Aaron Nelson (<a href="https://twitter.com/#!/sqlvariant" title="@SQLVariant on twitter" alt="@sqlvariant on twitter">twitter</a>|[blog][1]) is hosting [T-SQL Tuesday][2] this month with the topic of "Logging". Check out [his post][2] for a list of other T-SQL Tuesday posts this month and congratulate him on speaking at TechEd on Tuesday morning (he's denied being the loud powershell fan in the keynote, btw)._

## The Setup

Monitoring the sizes at the index and table level provides raw material for looking several different statistics. Luckily this is fairly easy to do for on premises and SQL Azure databases. 

_For the purpose of these examples I'm going to be logging the database growth in the same database. Obviously this won't work for every environment and adds an extra variable to our growth statistics._

First up, let's create a table to store the raw data in:

```sql
CREATE TABLE dbo.DatabaseSizeLog(
	Id		int IDENTITY(1,1) NOT NULL PRIMARY KEY,
	LogTime		DateTime2 NOT NULL,
	DatabaseId	int NOT NULL,
	ObjectId	int NOT NULL,
	TableName	varchar(200) NOT NULL,
	IndexId		int NULL,
	IndexName	varchar(200) NULL,
	AllocatedBytes	int NOT NULL,
	UsedBytes	int NOT NULL,
	UnusedBytes	int NOT NULL,
	[RowCount]	int NOT NULL
);
```
Note that I am capturing both ids and names for the table and index. We could JOIN to get the names, but being able to run a quick one line statement to look at key information is worth the slightly higher storage cost. I've also used an integer IDENTITY column as the required clustered index for a SQL Azure database. A better option would be LogTime, DatabaseId, and IndexId in the order that makes the most sense for how you intend to look at the data.

Once we have the raw table, we need the query to capture the data:

```sql
INSERT INTO dbo.DatabaseSizeLog(LogTime, DatabaseId, ObjectId, 
				TableName, IndexId, IndexName, AllocatedBytes, 
				UsedBytes, UnusedBytes, [RowCount])
SELECT    GetDate()
	, DB_ID()
	, o.object_id AS TableId
	, o.name
	, ps.index_id as [IndexId]
	, CASE WHEN ps.index_id > 1 THEN i.name ELSE NULL END AS IndexName
	, CAST(ps.reserved_page_count * 8192 AS DECIMAL(18,0)) AS Allocated
	, CAST(ps.used_page_count * 8192 AS DECIMAL(18,0)) AS Used
	, CAST((ps.reserved_page_count - used_page_count) * 8192 AS DECIMAL(18,0)) AS Unused
	, ps.row_count AS "RowCount"
FROM sys.dm_db_partition_stats ps
	INNER JOIN sys.objects o ON ps.object_id = o.object_id
	INNER JOIN sys.indexes i on i.index_id = ps.index_id AND i.object_id = ps.object_id
WHERE o.type NOT IN ('S','IT');
```
This should be placed in a scheduled job in SQL Agent for an on-premises database, or tied into a scheduled task system (perhaps via a worker role) for a SQL Azure database.

Now that we have some data and we're logging it at some regular interval, lets start getting some use out of it.

## Basic Information

Once we have some data in the table, we can build some information gathering queries to learn more about our data growth rates. 

**What are our largest tables right now?**

```sql
DECLARE @TargetTime DateTime;
SELECT TOP 1 @TargetTime = LogTime FROM dbo.DatabaseSizeLog ORDER BY LogTime DESC;

SELECT TOP 10
	TableName, 
	"Allocated (MB)" = CAST(SUM(AllocatedBytes) as DECIMAL(18,2)) / 1048576, 
	"Used in MB" = CAST(SUM(UsedBytes) as DECIMAL(18,2)) / 1048576, 
	"Unused in MB" = CAST(SUM(UnusedBytes) as DECIMAL(18,2)) / 1048576, 
	"Row Count" = MIN([RowCount])
FROM dbo.DatabaseSizeLog
WHERE LogTime = @TargetTime
GROUP BY TableName
ORDER BY SUM(AllocatedBytes) DESC;
```
**What percentage of our largest tables is indexes?**

```sql
DECLARE @TargetTime DateTime;
SELECT TOP 1 @TargetTime = LogTime FROM dbo.DatabaseSizeLog ORDER BY LogTime DESC;

SELECT TOP 10
	TableName, 
	"Total Allocated (MB)" = CAST(SUM(AllocatedBytes) as DECIMAL(18,2)) / 1048576, 
	"Percent For Indexes" = CAST(SUM(CASE WHEN IndexName IS NOT NULL THEN AllocatedBytes ELSE 0 END) as DECIMAL(18,2)) / CAST(SUM(AllocatedBytes) as DECIMAL(18,2)),
	"Row Count" = MIN([RowCount])
FROM dbo.DatabaseSizeLog
WHERE LogTime = @TargetTime
GROUP BY TableName
ORDER BY SUM(AllocatedBytes) DESC;
```
**Which tables are growing the quickest?**

```sql
WITH LogHistory AS (
	SELECT Id, LogTime, DatabaseId, ObjectId, 
			TableName, IndexId, IndexName, AllocatedBytes, 
			UsedBytes, UnusedBytes, [RowCount],
			ROW_NUMBER() OVER (PARTITION BY ObjectId, IndexId ORDER BY LogTime DESC)	AS PastTimeNumber
	FROM dbo.DatabaseSizeLog
)
SELECT 
		LH_NOW.TableName,
		PercentGrowth = CASE 
			WHEN SUM(LH_THEN.AllocatedBytes) = 0 AND SUM(LH_NOW.AllocatedBytes) = 0 THEN 0
			WHEN SUM(LH_THEN.AllocatedBytes) = 0 THEN 1
			ELSE 1 - SUM(LH_NOW.AllocatedBytes) / CAST(SUM(LH_THEN.AllocatedBytes) AS DECIMAL(18,2))
		END,
		BytesPerHour = (SUM(LH_NOW.AllocatedBytes) - SUM(LH_THEN.AllocatedBytes)) / (DateDiff(minute, LH_THEN.LogTime, LH_NOW.LogTime) / 60.0)
FROM LogHistory LH_NOW
	INNER JOIN LogHistory LH_THEN ON LH_NOW.ObjectId = LH_THEN.ObjectId AND LH_NOW.IndexId = LH_THEN.IndexId
WHERE LH_NOW.PastTimeNumber = 1
	AND LH_THEN.PastTimeNumber = 3	-- tweak this to widen or narrow the comparison time
GROUP BY LH_Now.TableName, LH_NOW.LogTime, LH_THEN.LogTime
ORDER BY SUM(LH_NOW.AllocatedBytes) - SUM(LH_THEN.AllocatedBytes) DESC
```
## Monitoring

Besides being able to look at some general statistics, if we have a system that can monitor the result of a SQL query, we have a number of options we can start monitoring against:

**Database size**
  
Most recent total allocated size of the database (in MB).

```sql
SELECT TOP 1 SUM(AllocatedBytes) / 1048576.0
FROM dbo.DatabaseSizeLog
GROUP BY LogTime
ORDER BY LogTime DESC;
```
**Hourly database growth for the last two entries**
  
Hourly growth in MB/hour for the last two entries

```sql
WITH LastTwoEntries AS (
	SELECT TOP 2 
			SizeInMB = SUM(AllocatedBytes) / 1048576.0,
			LogTime
	FROM dbo.DatabaseSizeLog
	GROUP BY LogTime
	ORDER BY LogTime DESC
)
SELECT (LTE1.SizeInMB - LTE2.SizeInMB) / (DATEDIFF(minute, LTE1.LogTime, LTE2.LogTime) / 60.0)
FROM LastTwoEntries LTE1
	INNER JOIN LastTwoEntries LTE2 ON LTE1.LogTime > LTE2.LogTime
```
**Wait, you deleted how many records?!?**
  
This lists all tables that have shrunk by more than 10% since the previous log entry.

```sql
WITH Entries AS (
	SELECT  ObjectId,
			TableName,
			SizeInMB = AllocatedBytes / 1048576.0,
			LogTime,
			ROW_NUMBER() OVER (PARTITION BY ObjectId ORDER BY LogTime DESC) AS Num
	FROM dbo.DatabaseSizeLog
	WHERE IndexName IS NULL
)
SELECT E1.TableName
FROM Entries E1
	INNER JOIN Entries E2 ON E1.ObjectId = E2.ObjectId
WHERE E1.Num = 1 
	AND E2.num = 2
	AND E2.SizeInMb <> 0
	AND E1.SizeInMb < E2.SizeInMb - (.10 * E2.SizeInMb);
```
## And more

By now I hope you have even more ideas for ways to slice this simple data set. Logging is a powerful tool in our inventory and often even a simple query can provide a great deal of information if we log it over time. The basic query that started this post probably didn't look like much, but capturing the values over time allowed us to expose a lot of new information about our database. Having visibility into how our systems are running can be the difference between finding out our database hit a size limit after the fact and knowing that it is growing at an abnormal pace long before the problem occurs.

 [1]: http://sqlvariant.com/ "Aaron's Blog"
 [2]: http://sqlvariant.com/2012/06/t-sql-tuesday-31-logging/ "T-SQL Tuesday #31 - Logging"