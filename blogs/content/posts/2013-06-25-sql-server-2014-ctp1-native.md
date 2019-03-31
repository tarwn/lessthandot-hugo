---
title: SQL Server 2014 CTP1 native compiled Hekaton procedures are faster than regular procedures
author: SQLDenis
type: post
date: 2013-06-25T08:01:00+00:00
excerpt: |
  I have been playing around with SQl Server 2014 Hekaton tables and stored procedures. I have noticed that native compiled stored procedures are between 20 and 40% faster than regular stored procedures
  
  If you want to test for yourself, then run the fo&hellip;
url: /index.php/datamgmt/dbprogramming/sql-server-2014-ctp1-native/
views:
  - 27253
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - hekaton
  - sql server 2014

---
I have been playing around with SQl Server 2014 Hekaton tables and stored procedures. I have noticed that native compiled stored procedures are between 20 and 40% faster than regular stored procedures

If you want to test for yourself, then run the following code
  
The codeblock below will create a database with a filegroup where we will store Hekaton tables

<pre>CREATE DATABASE HekatonDB
    ON 
    PRIMARY(NAME = [HekatonDB_data], 
			FILENAME = 'C:DBTestHekatonDBHekatonDB_data.mdf', size=500MB), 
    FILEGROUP [HekatonDB_fs_fg] CONTAINS MEMORY_OPTIMIZED_DATA
			(NAME = [HekatonDB_hk_fs_dir], 
			FILENAME = 'C:DBTestHekatonDBHekatonDB_hk_fs_dir')
 
	LOG ON (name = [HekatonDB_log], Filename='C:DBTestHekatonDBHekatonDB_log.ldf', size=500MB)
	COLLATE Latin1_General_100_BIN2;</pre>

Next, it is time to create the Hekaton table

<pre>USE HekatonDB
GO

	CREATE TABLE [dbo].[StorageTestTable]
( [Col1] int NOT NULL, 
  [Col2] char(100) NOT NULL,  

  CONSTRAINT [StorageTestTable_PK] PRIMARY KEY NONCLUSTERED HASH ([Col1]) WITH(BUCKET_COUNT = 1000000)
) WITH (MEMORY_OPTIMIZED = ON, 
 DURABILITY = SCHEMA_AND_DATA)
go</pre>

Lets insert some data, the following inserts 531441 rows in my table

<pre>insert [StorageTestTable]
select row_number() over(order by s1.name),replicate('a',100)
from sys.sysobjects s1, sys.sysobjects s2, sys.sysobjects s3</pre>

Let&#8217;s see how much memory SQL Server is using for this table

<pre>SELECT 
memory_allocated_for_table_kb   AS MemTableAllocKb,
memory_used_by_table_kb         AS MemTableUsedKb,
memory_allocated_for_indexes_kb AS MemIndexAllocKb,	
memory_used_by_indexes_kb       AS MemIndexUsedKb
FROM sys.dm_db_xtp_table_memory_stats
WHERE object_id = OBJECT_ID('StorageTestTable')</pre>

Here is the output

<pre>MemTableAllocKb	MemTableUsedKb	MemIndexAllocKb	MemIndexUsedKb
76096	        74733	        8192	        8192</pre>

As you can see it is about 75MB for the table and 8MB for the index
  

  
In order to create a native compiled stored procedure you need to do a couple of things different.
  
Native compiled stored procedures must be schema-bound
  
Execution context is required
  
Atomic blocks, create a transaction if there is none, otherwise, create a savepoint
  
Session settings are fixed at create time

Here is our native compiled stored procedure

<pre>CREATE PROCEDURE usp_TestHekatonNative
WITH NATIVE_COMPILATION,                          -- native compiled
EXECUTE AS OWNER,                                 -- Execution context
SCHEMABINDING                                     -- schema-bound
AS 
BEGIN ATOMIC WITH                                 -- atomic block
(
      TRANSACTION ISOLATION LEVEL = SNAPSHOT,     -- Session settings
      LANGUAGE = 'english'                        -- Session settings
)

	SELECT	COUNT(*)
	FROM	dbo.[StorageTestTable]

END
GO</pre>

And here is our regular stored procedure

<pre>CREATE PROCEDURE usp_TestHekatonRegular
AS 
	SELECT	COUNT(*)
	FROM	dbo.[StorageTestTable]
GO</pre>

Time to run the tests

Enable time statistics

<pre>SET STATISTICS TIME ON</pre>

Run these two procs a bunch of times

<pre>exec usp_TestHekatonNative</pre>

<pre>exec usp_TestHekatonRegular</pre>

Does the native compiled stored procedure run between 20 and 40% faster on your systems as well?

Please post your numbers in the comment section