---
title: How do you manage SQL Agent Jobs when using mirroring?
author: SQLDenis
type: post
date: 2013-01-25T12:56:00+00:00
ID: 1937
excerpt: 'I have a bunch of SQL Agent jobs that execute T-SQL against databases. A bunch of these databases are mirrored. Of course if the database is the principal then these jobs will work without a problem. But what happens if you failover? Now these jobs will&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/how-do-you-manage-sql/
views:
  - 12534
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - ha
  - ha/dr
  - jobs
  - maintenance
  - mirroring

---
I have a bunch of SQL Agent jobs that execute T-SQL against databases. A bunch of these databases are mirrored. Of course if the database is the principal then these jobs will work without a problem. But what happens if you failover? Now these jobs will start failing. You can either have the same jobs on both servers and have them enabled or disabled depending where the mirror or principal is. You can very easy enable or disable these whenever you failover. For example

sql
USE msdb
GO

UPDATE sysjobs
SET enabled =1
WHERE name IN ('',''....) --use a table that has all the jobs instead
```

Or another way would be to check if the database is online to see if the job should continue running

Here is such an example

sql
IF EXISTS(
SELECT  1  FROM sys.databases
WHERE state_desc = 'ONLINE'
AND collation_name IS NOT NULL
AND name = 'YourDB')
BEGIN
PRINT 'yep, good to go'
END
```

The reason we also check for collation\_name in addition to state\_desc is documented in [Books On Line][1]

> A database that has just come online is not necessarily ready to accept connections. To identify when a database can accept connections, query the collation\_name column of sys.databases or the Collation property of DATABASEPROPERTYEX. The database can accept connections when the database collation returns a non-null value. For AlwaysOn databases, query the database\_state or database\_state\_desc columns of sys.dm\_hadr\_database\_replica\_states.

Yet another option would be to have a table with the 'live' server for the database, this however is more used for jobs, SSIS packages and programs that live on other servers

How do you manage your jobs when dealing with mirroring? Leave me a comment, I am interested in your approach.

 [1]: http://technet.microsoft.com/en-us/library/ms178534.aspx