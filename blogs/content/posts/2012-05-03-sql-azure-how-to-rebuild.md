---
title: SQL Azure How To Rebuild A Clustered Index
author: SQLDenis
type: post
date: 2012-05-03T23:52:00+00:00
ID: 1616
excerpt: |
  In yesterday's post A couple of things to be aware of when working with tables in SQL Azure I showed you some things to be aware of in regards to tables. Today let's talk about how to rebuild an index
  
  Let's say you have a table with a clustered index&hellip;
url: /index.php/datamgmt/datadesign/sql-azure-how-to-rebuild/
views:
  - 12551
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - azure
  - cloud computing
  - fragmentation
  - sql azure

---
In yesterday's post [A couple of things to be aware of when working with tables in SQL Azure][1] I showed you some things to be aware of in regards to tables. Today let's talk about how to rebuild an index

Let's say you have a table with a clustered index on a uniqueidentifier column, you know that you have to use newid() instead of newsequentialid() since newsequentialid() is not supported. Clustering with newid() is not recommended because you will get page splits. Let's take a look at how we can recreate the index

First create the following table with a clustered index

sql
CREATE TABLE Test(bla uniqueidentifier default newid())

CREATE CLUSTERED INDEX ix_Test_bla on Test(bla)
GO
```

Now run the following block of code

sql
;WITH CTE AS (SELECT 1 AS A 
			UNION ALL SELECT 2 
			UNION ALL SELECT 3 
			UNION ALL SELECT 4 
			UNION ALL SELECT 5
			UNION ALL SELECT 6)
INSERT Test
SELECT newid() 
FROM CTE a
CROSS JOIN CTE b
CROSS JOIN CTE c
CROSS JOIN CTE d
GO 5
```

Beginning execution loop

(1296 row(s) affected)

(1296 row(s) affected)

(1296 row(s) affected)

(1296 row(s) affected)

(1296 row(s) affected)
  
Batch execution completed 5 times.

Let's see how fragmented the index is

sql
SELECT Object_name(s.object_id) as Tablename,s.name as Indexname
,index_type_desc
,avg_fragmentation_in_percent
,page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') d
join sys.indexes s on d.object_id = s.object_id
and d.index_id = s.index_id
and s.name = 'ix_Test_bla'
```

<pre>Tablename	Indexname	index_type_desc	avg_fragmentation_in_percent	page_count
Test	        ix_Test_bla	CLUSTERED INDEX	34.4827586206897                 58</pre>

Now it is time to 'fix' the index, we can either defragment/reorganize or rebuild the index

sql
ALTER INDEX ix_Test_bla ON [dbo].[Test] REORGANIZE
```

_Msg 40517, Level 16, State 1, Line 1
  
Keyword or statement option 'REORGANIZE' is not supported in this version of SQL Server._

Okay, so SQL Azure does not support reorganizing the index
  
What if we drop the index and then create it again?

sql
DROP INDEX [ix_Test_bla] ON [dbo].[Test] 
GO
```

_Msg 40054, Level 16, State 2, Line 1
  
Tables without a clustered index are not supported in this version of SQL Server. Please create a clustered index and try again.
  
The statement has been terminated._

Mmmm, not supported either, let's try creating the index with the drop_existing clause

sql
CREATE CLUSTERED INDEX [ix_Test_bla] ON [dbo].[Test]
(
	[bla] ASC
)WITH (DROP_EXISTING = ON)
GO
```

That worked, let's check fragmentation again

sql
SELECT Object_name(s.object_id) as Tablename,s.name as Indexname
,index_type_desc
,avg_fragmentation_in_percent
,page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') d
join sys.indexes s on d.object_id = s.object_id
and d.index_id = s.index_id
and s.name = 'ix_Test_bla'
```

<pre>Tablename	Indexname	index_type_desc	avg_fragmentation_in_percent	page_count
Test	        ix_Test_bla	CLUSTERED INDEX	4.76190476190476                21
</pre>

Mmmm, didn't get rid of all the fragmentation. What happens if we try to do the same again?

sql
CREATE CLUSTERED INDEX [ix_Test_bla] ON [dbo].[Test]
(
	[bla] ASC
)WITH (DROP_EXISTING = ON)
GO
```

sql
SELECT Object_name(s.object_id) as Tablename,s.name as Indexname
,index_type_desc
,avg_fragmentation_in_percent
,page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') d
join sys.indexes s on d.object_id = s.object_id
and d.index_id = s.index_id
and s.name = 'ix_Test_bla'
```

<pre>Tablename	Indexname	index_type_desc	avg_fragmentation_in_percent	page_count
Test	        ix_Test_bla	CLUSTERED INDEX	9.52380952380952                 21
</pre>

Still didn't get rid of all fragmentation
  
Let's try something else, first we are going to add some more data

sql
;WITH CTE AS (SELECT 1 AS A 
			UNION ALL SELECT 2 
			UNION ALL SELECT 3 
			UNION ALL SELECT 4 
			UNION ALL SELECT 5
			UNION ALL SELECT 6)
INSERT Test
SELECT newid() 
FROM CTE a
CROSS JOIN CTE b
CROSS JOIN CTE c
CROSS JOIN CTE d
go 5
```

Let's check fragmentation

sql
SELECT Object_name(s.object_id) as Tablename,s.name as Indexname
,index_type_desc
,avg_fragmentation_in_percent
,page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') d
join sys.indexes s on d.object_id = s.object_id
and d.index_id = s.index_id
and s.name = 'ix_Test_bla'
```

<pre>Tablename	Indexname	index_type_desc	avg_fragmentation_in_percent	page_count
Test	        ix_Test_bla	CLUSTERED INDEX	72.8395061728395                 81
</pre>

Oh yeah, that is real bad

Now let's rebuild the index instead

sql
ALTER INDEX ix_Test_bla ON [dbo].[Test] REBUILD
```

Check fragmentation again

sql
SELECT Object_name(s.object_id) as Tablename,s.name as Indexname
,index_type_desc
,avg_fragmentation_in_percent
,page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') d
join sys.indexes s on d.object_id = s.object_id
and d.index_id = s.index_id
and s.name = 'ix_Test_bla'
```

<pre>Tablename	Indexname	index_type_desc	avg_fragmentation_in_percent	page_count
Test	        ix_Test_bla	CLUSTERED INDEX	0                               41</pre>

And just like that all the fragmentation is gone…..good times

 [1]: /index.php/DataMgmt/DataDesign/a-couple-of-things-to