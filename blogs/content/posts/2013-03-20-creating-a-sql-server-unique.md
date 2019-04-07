---
title: Creating a SQL Server Unique Index that behaves like an Oracle Unique Index
author: SQLDenis
type: post
date: 2013-03-20T11:19:00+00:00
ID: 2039
excerpt: "In yesterday's post Unique index difference between Oracle and SQL Server , I showed you that SQL Server only allows one NULL value in an unique index while Oracle allows multiple NULL values. Today we are going to look how we can allow multiple NULL va&hellip;"
url: /index.php/datamgmt/dbprogramming/creating-a-sql-server-unique/
views:
  - 21050
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
In yesterday&#8217;s post [Unique index difference between Oracle and SQL Server][1] , I showed you that SQL Server only allows one NULL value in an unique index while Oracle allows multiple NULL values. Today we are going to look how we can allow multiple NULL values as well in a SQL Server unique index. I am going to show you two techniques. The first technique is known as a nullbuster, this was first demonstrated I believe by former SQl Server MVP Steve Kass. Basically you use a computed column to allow for multiple NULLs. Here is an example

sql
CREATE TABLE TestUnique (
pk int identity(1,1) primary key,
ID  int NULL,
nullbuster as (CASE WHEN ID IS NULL THEN pk ELSE 0 END),
CONSTRAINT uc_TestUnique UNIQUE (ID,nullbuster)
)
```

Now insert 3 rows, two of them being NULL

sql
INSERT TestUnique VALUES(1)
INSERT TestUnique VALUES(null)
INSERT TestUnique VALUES(null)
```

That worked without a problem. Now let&#8217;s insert the value 1 again

sql
INSERT TestUnique VALUES(1)
```

As expected that blows up

Msg 2627, Level 14, State 1, Line 1
  
Violation of UNIQUE KEY constraint &#8216;uc_TestUnique&#8217;. Cannot insert duplicate key in object &#8216;dbo.TestUnique&#8217;. The duplicate key value is (1, 0).
  
The statement has been terminated.

This will return the row with the value 1

sql
SELECT ID from TestUnique
WHERE ID =1
```

This will return the rows with the value NULL

sql
SELECT ID from TestUnique
WHERE ID IS NULL
```

Now what do you think this will return? 🙂

sql
SELECT ID from TestUnique
WHERE ID <>1
```

If you want to know why, look at number 7 here: [SQL Server Quiz, Can You Answer All These?][2]

Drop the table

sql
DROP TABLE TestUnique
```
The second way we can add an index with multiple NULL values is by using a filtered index. I already covered filtered indexes in this post [Filtered Indexes][3] as part of the [SQL Advent 2011 calendar][4]

Let&#8217;s see how we can do this. Create the unique table again

sql
CREATE TABLE TestUnique (Id int)
```

Here is how you create the filtere index, it is pretty much a regular index with an additional WHERE clause. 

sql
CREATE UNIQUE INDEX SomeIndex ON TESTUNIQUE (ID)
WHERE ID IS NOT NULL;
```

What we are telling SQL Server is to index everything that is not NULL

Insert these 3 rows

sql
INSERT INTO TestUnique VALUES(1);
INSERT INTO TestUnique VALUES(null);
INSERT INTO TestUnique VALUES(null);
```

If you try to insert a value of 1 again, you will get an error

sql
INSERT INTO TestUnique VALUES(1);
```

_Msg 2601, Level 14, State 1, Line 1
  
Cannot insert duplicate key row in object &#8216;dbo.TestUnique&#8217; with unique index &#8216;SomeIndex&#8217;. The duplicate key value is (1).
  
The statement has been terminated._

Now let&#8217;s select from the table

sql
SELECT * FROM TestUnique;
```

Here are the results
  
Id
  
&#8212;&#8211;
  
1
  
NULL
  
NULL

There are two rows with the value NULL in the result set. As you can see using a filtered index enables you to create an index which mimics the behavior from a unique index in Oracle

 [1]: /index.php/DataMgmt/DBProgramming/unique-index-difference-between-oracle
 [2]: /index.php/DataMgmt/DataDesign/sql-server-quiz-can-you-answer-all-these
 [3]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-19
 [4]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap