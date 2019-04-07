---
title: 'SQL Advent 2012 Day 8: Foreign Keys'
author: SQLDenis
type: post
date: 2012-12-08T15:17:00+00:00
ID: 1836
excerpt: |
  This is day eight of the SQL Advent 2012 series of blog posts. Today we are going to look at foreign keys
  
    
  That is all for day eight of the SQL Advent 2012 series, come back tomorrow for the next one, you can also check out all the posts from last&hellip;
url: /index.php/datamgmt/dbprogramming/foreign-keys/
views:
  - 8478
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - constraints
  - data integrity
  - foreign keys
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms

---
This is day eight of the [SQL Advent 2012 series][1] of blog posts. In yesterday&#8217;s post [SQL Advent 2012 Day 7: Lack of constraints][2] we touched a little upon foreign key constraints but today we are going to take a closer look at foreign keys. The two things that we are going to cover are the fact that you don&#8217;t need a primary key in order to define a foreign key relationship, SQL Server by default will not index foreign keys

## You don&#8217;t need a primary key in order to have a foreign key

Most people will define a foreign key relationship between the foreign key and a primary key. You don&#8217;t have to have a primary key in order to have a foreign key, if you have a unique index or a unique constraint then those can be used as well. Let&#8217;s take a look at what that looks like with some code examples

**A foreign key with a unique constraint instead of a primary key**
  
First create a table to which we will add a unique constraint after creation

sql
CREATE TABLE TestUniqueConstraint(id int)
GO
```

Add a unique constraint to the table

sql
ALTER TABLE TestUniqueConstraint ADD CONSTRAINT ix_unique UNIQUE (id)
GO
```

Insert a value of 1, this should succeed

sql
INSERT  TestUniqueConstraint VALUES(1)
GO
```

Insert a value of 1 again, this should fail

sql
INSERT  TestUniqueConstraint VALUES(1)
GO
```

_Msg 2627, Level 14, State 1, Line 2
  
Violation of UNIQUE KEY constraint &#8216;ix_unique&#8217;. Cannot insert duplicate key in object &#8216;dbo.TestUniqueConstraint&#8217;. The duplicate key value is (1).
  
The statement has been terminated._

Now that we verified that we can&#8217;t have duplicates, it is time to create the table that will have the foreign key

sql
CREATE TABLE TestForeignConstraint(id int)
GO
```

Add the foreign key to the table

sql
ALTER TABLE dbo.TestForeignConstraint ADD CONSTRAINT
	FK_TestForeignConstraint_TestUniqueConstraint FOREIGN KEY
	(id) REFERENCES dbo.TestUniqueConstraint(id) 
```

Insert a value that exist in the table that is referenced by the foreign key constraint

sql
INSERT TestForeignConstraint  VALUES(1)
INSERT TestForeignConstraint  VALUES(1)
```

Insert a value that does not exist in the table that is referenced by the foreign key constraint

sql
INSERT TestForeignConstraint  VALUES(2)
```

_Msg 547, Level 16, State 0, Line 1
  
The INSERT statement conflicted with the FOREIGN KEY constraint &#8220;FK\_TestForeignConstraint\_TestUniqueConstraint&#8221;. The conflict occurred in database &#8220;tempdb&#8221;, table &#8220;dbo.TestUniqueConstraint&#8221;, column &#8216;id&#8217;.
  
The statement has been terminated._

As you can see, you can&#8217;t insert the value 2 since it doesn&#8217;t exist in the TestUniqueConstraint table

**A foreign key with a unique index instead of a primary key**
  
This section will be similar to the previous section, the difference is that we will use a unique index instead of a unique constraint

First create a table to which we will add a unique index after creation

sql
CREATE TABLE TestUniqueIndex(id int)
GO
```

Add the unique index

sql
CREATE UNIQUE NONCLUSTERED INDEX ix_unique ON TestUniqueIndex(id)
GO
```

Insert a value of 1, this should succeed

sql
INSERT  TestUniqueIndex VALUES(1)
GO
```

Insert a value of 1 again , this should now fail

sql
INSERT  TestUniqueIndex VALUES(1)
GO
```

_Msg 2601, Level 14, State 1, Line 2
  
Cannot insert duplicate key row in object &#8216;dbo.TestUniqueIndex&#8217; with unique index &#8216;ix_unique&#8217;. The duplicate key value is (1).
  
The statement has been terminated._

Now that we verified that we can&#8217;t have duplicates, it is time to create the table that will have the foreign key

sql
CREATE TABLE TestForeignIndex(id int)
GO
```

Add the foreign key constraint

sql
ALTER TABLE dbo.TestForeignIndex ADD CONSTRAINT
	FK_TestForeignIndex_TestUniqueIndex FOREIGN KEY
	(id) REFERENCES dbo.TestUniqueIndex(id) 
```

Insert a value that exist in the table that is referenced by the foreign key constraint

sql
INSERT TestForeignIndex  VALUES(1)
INSERT TestForeignIndex  VALUES(1)
```

Insert a value that does not exist in the table that is referenced by the foreign key constraint

sql
INSERT TestForeignIndex  VALUES(2)
```

_Msg 547, Level 16, State 0, Line 1
  
The INSERT statement conflicted with the FOREIGN KEY constraint &#8220;FK\_TestForeignIndex\_TestUniqueIndex&#8221;. The conflict occurred in database &#8220;tempdb&#8221;, table &#8220;dbo.TestUniqueIndex&#8221;, column &#8216;id&#8217;.
  
The statement has been terminated._

As you can see, you can&#8217;t insert the value 2 since it doesn&#8217;t exist in the TestUniqueIndex table

As you have seen with the code example, you can have a foreign key constraint that will reference a unique index or a unique constraint in addition to be able to reference a primary key

## Foreign keys are not indexed by default

When you create a primary key, SQL Server will by default make that a clustered index. When you create a foreign key, there is no index created

Scroll up to where we added the unique constraint to the TestUniqueConstraint table, you will see this code

sql
ALTER TABLE TestUniqueConstraint ADD CONSTRAINT ix_unique UNIQUE (id)
```

All we did was add the constraint, SQL Server added the index behind the scenes for us in order to help enforce uniqueness more efficiently

Now run this query below

sql
SELECT OBJECT_NAME(object_id) as TableName,
name as IndexName, 
type_desc as StorageType
FROM sys.indexes
WHERE OBJECT_NAME(object_id) IN('TestUniqueIndex','TestUniqueConstraint')
AND name IS NOT NULL
```

You will get these results

<pre>TableName	        IndexName	StorageType
---------------------   -----------     --------------
TestUniqueConstraint	ix_unique	NONCLUSTERED
TestUniqueIndex	        ix_unique	NONCLUSTERED</pre>

As you can see both tables have an index

Now let&#8217;s look at what the case is for the foreign key tables. Run the query below

sql
SELECT OBJECT_NAME(object_id) as TableName,
name as IndexName, 
type_desc as StorageType
FROM sys.indexes
WHERE OBJECT_NAME(object_id) IN('TestForeignIndex','TestForeignConstraint')
```

Here are the results for that query

<pre>TableName	      IndexName	StorageType
--------------------- --------- -------------
TestForeignConstraint	NULL	HEAP
TestForeignIndex	NULL	HEAP
</pre>

As you can see no indexes have been added to the tables. Should you add indexes? In order to answer that let&#8217;s see what would happen if you did add indexes. Joins would perform faster since it can traverse the index instead of the whole table to find the matching join conditions. Updates and deletes will be faster as well since the index can be used to find the foreign keys rows to update or delete (remember this depends if you specified CASCADE or NO ACTION when you create the foreign key constraint)
  
So to answer the question, yes, I think you should index the foreign key columns

That is all for day eight of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][3]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/lack-of-constraints
 [3]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap