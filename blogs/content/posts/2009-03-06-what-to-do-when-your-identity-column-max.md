---
title: What To Do When Your Identity Column Maxes Out
author: Erik
type: post
date: 2009-03-06T22:27:51+00:00
ID: 343
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/what-to-do-when-your-identity-column-max/
views:
  - 13148
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
You have an identity column in your database that was created as int identity(1,1). But after a good few years you are approaching the limit for a signed four-byte long integer, 2147483647. What do you do? Switch the column to bigint?

Not yet!

You do something like this:

sql
--What's the biggest signed four-byte long:
--select power(convert(bigint, 2), 31) - 1
--2147483647
 
--what your table looks like
CREATE TABLE tester (
    testerid INT IDENTITY(1, 1) not null CONSTRAINT pk_tester PRIMARY KEY CLUSTERED,
    descr VARCHAR(100)
)
-- simulate the identity column being near the end of its life (only 32 more rows will fit)
DBCC checkident(tester, reseed, 2147483616)
 
-- push the identity column to its absolute limit
INSERT tester SELECT CONVERT(VARCHAR(100), newid())
WHILE @@ROWCOUNT < 16
    INSERT tester SELECT CONVERT(VARCHAR(100), newid()) FROM tester
SELECT * FROM tester
 
-- you can see that you can't insert another one like so:
-- insert tester select convert(varchar(100), newid())
 
--Redefine the identity column by moving tables in and out.
-- * would have to drop referential constraints temporarily
-- * but you can generate script to drop and recreate referential constraints
 
-- move the table out of the way of the new table
EXEC SP_RENAME 'tester', 'oldtester'
ALTER TABLE oldtester DROP CONSTRAINT pk_tester
 
-- create the new table with identity set to crawl downward from 0
CREATE TABLE tester (
    testerid INT IDENTITY(1, -1) not null CONSTRAINT pk_tester PRIMARY KEY CLUSTERED, -- I have no idea why it's not identity(0, -1), but it's not.
    descr VARCHAR(100)
)
 
-- insert the data from the old table
SET IDENTITY_INSERT tester ON
INSERT tester (testerid, descr)
SELECT testerid, descr FROM oldtester
SET IDENTITY_INSERT tester OFF
 
-- insert some rows so we can see if it works
INSERT tester SELECT CONVERT(VARCHAR(100), newid()) FROM tester
 
-- and it works! New rows will start at 0 and count downward to -1, -2, and so on.
SELECT * FROM tester
 
-- clean up
DROP TABLE tester
DROP TABLE oldtester
```

With this solution, you can keep your database going for another few years, or for about as long as it took you to fill up the positive integers. Then you&#8217;ll have to switch to bigints or a decimal data type, though I have to express my disgust for using a packed data type as a clustered index key, especially considering that compared to int or bigint, decimal always uses more bytes of storage for the same precision:

int: 8-9 digits, up to 4294967296 = 4 bytes storage
  
decimal: 9 digits = 5 bytes storage
  
bigint: 19-20 digits, up to 18446744073709551616 = 8 bytes storage
  
decimal: 10-19 digits = 9 bytes storage
  
decimal: 20-28 digits = 13 bytes storage
  
decimal: 29-38 digits = 17 bytes storage

So you can see that bigint is far superior to decimal until you get to 20 digits, supporting up to 18.4 quintillion unique values, which is a heck of a lot, for less than decimal would take to get you there.