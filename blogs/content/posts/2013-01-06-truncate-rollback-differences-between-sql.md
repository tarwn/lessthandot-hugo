---
title: Truncate rollback differences between SQL Server and Oracle
author: SQLDenis
type: post
date: 2013-01-06T22:27:00+00:00
ID: 1902
excerpt: |
  I wrote a blogpost about the fact that there is a common myth that you can't rollback a truncate statement in SQL this post was written on June 13, 2007 and it showed you that you could rollback a truncate. Here is some code that shows that.
  
  CREATE&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/truncate-rollback-differences-between-sql/
views:
  - 7975
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - Oracle
  - Oracle Admin
tags:
  - gotcha
  - oracle
  - sql server
  - tip
  - truncate

---
I wrote a blogpost about the fact that there is a common myth that you can't rollback a truncate statement in SQL [this post was written on June 13, 2007][1] and it showed you that you could rollback a truncate. Here is some code that shows that.

First create this very simple table

sql
CREATE TABLE dbo.TruncateTest (ID int IDENTITY PRIMARY KEY, 
				SomeOtherCol varchar(49))
GO
```

Add the following two rows

sql
INSERT dbo.TruncateTest VALUES(1)
INSERT dbo.TruncateTest VALUES(1)
```

Now execute this whole block in one shot, you will see three resultsets, two of them will have two rows and one resultset will be empty

sql
SELECT * FROM dbo.TruncateTest -- 2 rows
 
BEGIN TRAN
    TRUNCATE TABLE dbo.TruncateTest
    SELECT * FROM dbo.TruncateTest -- 0 rows
ROLLBACK TRAN
 
SELECT * FROM dbo.TruncateTest  -- 2 rows again after rollback
```

Here is the output

<pre>ID          SomeOtherCol
----------- -------------------------------------------------
1           1
2           1

(2 row(s) affected)

ID          SomeOtherCol
----------- -------------------------------------------------

(0 row(s) affected)

ID          SomeOtherCol
----------- -------------------------------------------------
1           1
2           1

(2 row(s) affected)
</pre>

As you can see the table was empty at one point, however the table has the same two rows again, if you execute this query, you will see those two row again

sql
SELECT * FROM dbo.TruncateTest
```

## What about Oracle, can you rollback a truncate statement?

In SQL Server the minimum permission required is ALTER on table\_name. TRUNCATE TABLE permissions default to the table owner, members of the sysadmin fixed server role, and the db\_owner and db_ddladmin fixed database roles, I blogged about this as well in the post [Why do you need additional privileges for truncate table compared to delete?][2] 

In Oracle to truncate a table, the table must be in your schema or you must have DROP ANY TABLE system privilege.

In Oracle a truncate statement is actually a DDL statement, you CANNOT rollback a truncate after it has happened. A truncate statement removes all rows and returns the freed space to the tablespace containing the table.

Please keep these differences in mind when working with different platforms, don't assume anything, it will bite you in the butt, always reference the documentation. For some more Oracle and SQL Server differences, see also my post [Differences between Oracle and SQL Server when working with NULL and blank values][3]

 [1]: http://sqlblog.com/blogs/denis_gobo/archive/2007/06/13/1458.aspx
 [2]: /index.php/DataMgmt/DBProgramming/MSSQLServer/why-do-you-need-additional
 [3]: /index.php/DataMgmt/DBProgramming/Oracle/differences-between-oracle-and-sql