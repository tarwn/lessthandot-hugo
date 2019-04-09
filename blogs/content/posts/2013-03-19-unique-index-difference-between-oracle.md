---
title: Unique index difference between Oracle and SQL Server
author: SQLDenis
type: post
date: 2013-03-19T21:55:00+00:00
ID: 2038
excerpt: |
  When working with different database systems you have to be aware that some things work differently from one system to another. I already blogged a couple of times about differences between SQl Server and Oracle, those post are the following
  Truncate r&hellip;
url: /index.php/datamgmt/dbprogramming/unique-index-difference-between-oracle/
views:
  - 10833
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - Oracle
  - Oracle Admin
tags:
  - gotcha
  - 'null'
  - oracle
  - sql server
  - tip

---
When working with different database systems you have to be aware that some things work differently from one system to another. I already blogged a couple of times about differences between SQl Server and Oracle, those post are the following
  
[Truncate rollback differences between SQL Server and Oracle][1]
  
[Differences between Oracle and SQL Server when working with NULL and blank values][2]
  
[An Oracle NULL/Blank gotcha when coming from SQL Server][3]. 

Today we are going to look at the difference between a unique index in Oracle and in SQL Server.

Let's start with SQL Server. First create this table and also this index

sql
CREATE TABLE TestUnique (Id int)


CREATE UNIQUE INDEX SomeIndex ON TESTUNIQUE (ID);
```

Insert the following two rows

sql
INSERT INTO TestUnique VALUES(1);
INSERT INTO TestUnique VALUES(null);
```

Now let's insert one more NULL

sql
INSERT INTO TestUnique VALUES(null);
```

Here is the error
  
_Msg 2601, Level 14, State 1, Line 1
  
Cannot insert duplicate key row in object &#8216;dbo.TestUnique' with unique index &#8216;SomeIndex'. The duplicate key value is (<null>).
  
The statement has been terminated.</null>_

As you can see you can only have one NULL value in the table

What about Oracle? Let's take a look. Run this whole block, it will create a table, a unique index and will insert the same data
  


```plsql
CREATE TABLE TestUnique (Id int);

CREATE UNIQUE INDEX INDEX1 ON TESTUNIQUE (ID);

INSERT INTO TestUnique VALUES(1);
INSERT INTO TestUnique VALUES(null);
INSERT INTO TestUnique VALUES(null);

SELECT * FROM TestUnique;
```

Here is what it looks like from SQL developer

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleSQLOutput.PNG?mtime=1363737088"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleSQLOutput.PNG?mtime=1363737088" width="379" height="377" /></a>
</div>

As you can see SQL Server only allows one NULL value while Oracle allows multiple NULL values. You have to be aware of these differences otherwise you might get unintended behavior from your programs

If you try to insert the value 1 again, you will get the following error

Error report:

<pre>SQL Error: ORA-00001: unique constraint (SYSTEM.INDEX1) violated
00001. 00000 -  "unique constraint (%s.%s) violated"
*Cause:    An UPDATE or INSERT statement attempted to insert a duplicate key.
           For Trusted Oracle configured in DBMS MAC mode, you may see
           this message if a duplicate entry exists at a different level.
*Action:   Either remove the unique restriction or do not insert the key.</pre>

Come back tomorrow to see how you can create an index in SQL Server that will allow multiple NULL values. You can find that post here: [Creating a SQL Server Unique Index that behaves like an Oracle Unique Index][4]

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/truncate-rollback-differences-between-sql
 [2]: /index.php/DataMgmt/DBProgramming/Oracle/differences-between-oracle-and-sql
 [3]: /index.php/DataMgmt/DBProgramming/Oracle/an-oracle-null-blank-gotcha
 [4]: /index.php/DataMgmt/DBProgramming/MSSQLServer/creating-a-sql-server-unique