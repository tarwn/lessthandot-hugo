---
title: Listing tables that are truly partitioned in SQL Server
author: SQLDenis
type: post
date: 2013-02-23T08:59:00+00:00
ID: 2007
excerpt: |
  To list all the tables that are partitioned you can use the sys.partitions view. However be aware that all tables and indexes in SQL Server contain at least one partition, whether or not they are explicitly partitioned.
  
  If you were to do the followin&hellip;
url: /index.php/datamgmt/dbprogramming/listing-tables-that-are-truly/
views:
  - 15700
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - features
  - partitioning
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms

---
To list all the tables that are partitioned you can use the sys.partitions view. However be aware that all tables and indexes in SQL Server contain at least one partition, whether or not they are explicitly partitioned.

If you were to do the following, you would get back every table

sql
SELECT partition_number,rows,object_name(object_id)
FROM sys.partitions
```

So what can you do? Let's take a look. First we are going to create a partitioned table in case you don't have one so that you can get the same output as me.

sql
CREATE TABLE SalesPartitioned(YearCol SMALLINT NOT NULL,OrderID INT NOT NULL, SomeData UNIQUEIDENTIFIER DEFAULT newsequentialid())
GO
```

We are going to insert some data into the table

sql
INSERT SalesPartitioned (YearCol,OrderID)
SELECT 2007,number
FROM master..spt_values
WHERE type = 'P'
UNION ALL
SELECT 2008,number + 2048
FROM master..spt_values
WHERE type = 'P'
UNION ALL
SELECT 2009,number + 4096
FROM master..spt_values
WHERE type = 'P'
```

We will now add a primary key

sql
ALTER TABLE dbo.SalesPartitioned ADD CONSTRAINT
	PK_Sales PRIMARY KEY NONCLUSTERED (YearCol,OrderID)
```

Now it is time to create our partition function. Here is how we will do it

sql
CREATE PARTITION FUNCTION pfFiscalYear(SMALLINT)
AS RANGE RIGHT FOR VALUES(2007,2008,2009)
```

What that does is actually create 4 partitions, one for 2007, one for 2008, one for everything after 2008, and one for everything before 2006.
  
<=2006 = 2007 = 2008 >= 2009

You can verify this by using the function $partition

sql
select 1 AS val,$partition.pfFiscalYear(1) AS partition	    UNION all
select 2006,$partition.pfFiscalYear(2006)	UNION all
select 2007,$partition.pfFiscalYear(2007)	UNION all
select 2008,$partition.pfFiscalYear(2008)	UNION all
select 2009,$partition.pfFiscalYear(2009)	UNION all
select 2010,$partition.pfFiscalYear(2010)	UNION all
select 3000,$partition.pfFiscalYear(3000)
```

And here is the output

<pre>val         partition
----------- -----------
1           1
2006        1
2007        2
2008        3
2009        4
2010        4
3000        4</pre>

Now that we have the partition function, we need a partition scheme. A partition scheme is used to map boundary values in partition functions to filegroups. You can have one filegroup for each year placed on a different spindle, this way you don't have to wait for the disk if all partitions are on the same spindle. For the sake of simplicity we only have one filegroup. Here is how to create the partition scheme

sql
CREATE PARTITION SCHEME psFiscalYear
AS PARTITION pfFiscalYear ALL TO ([PRIMARY])
```

_Partition scheme 'psFiscalYear' has been created successfully. 'PRIMARY' is marked as the next used filegroup in partition scheme 'psFiscalYear'._

Now we will add a clustered index and partition this on the YearCol column, the syntax looks like this

sql
CREATE CLUSTERED INDEX IX_Sales ON SalesPartitioned(YearCol,OrderID)
ON psFiscalYear(YearCol)
```

Now it is time to list all the tables that are partitioned. Here is how you do it

sql
SELECT partition_number,rows,object_name(object_id)
FROM sys.partitions s
WHERE EXISTS(SELECT NULL 
				FROM sys.partitions s2 
				WHERE s.object_id = s2.object_id 
				AND partition_number > 1
				AND s.index_id = s2.index_id)
```

Here is the output

<pre>partition_number	rows	TableName
1	                0	SalesPartitioned
2	                2048	SalesPartitioned
3	                2048	SalesPartitioned
4	                2048	SalesPartitioned</pre>

As you can see we used WHERE EXISTS, we checked that the object had a partition\_number higher than two and we also matched on index\_id since a table can have more than one index