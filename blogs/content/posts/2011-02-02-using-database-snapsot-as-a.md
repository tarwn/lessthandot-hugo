---
title: Using Database Snapshot As A Protection Against Rogue Deletes
author: SQLDenis
type: post
date: 2011-02-02T10:50:00+00:00
ID: 1027
excerpt: 'Let me first start by saying that this is not a foolproof solution, it is just another way that could help you out when you by mistake delete some data. If someone deletes the data in the table just before you create the database snapshot then you will&hellip;'
url: /index.php/datamgmt/datadesign/using-database-snapsot-as-a/
views:
  - 3330
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - delete
  - snapshot
  - sql server 2005
  - sql server 2008

---
Let me first start by saying that this is not a foolproof solution, it is just another way that could help you out when you by mistake delete some data. If someone deletes the data in the table just before you create the database snapshot then you will be out of luck.

The option in this article is also nice if your backups are terabyte size because your restore would take a while and even then all the tables would be affected. You would have to restore to another DB and then just pull down the data from the table you want.

Everyday we create a database snapshot on our mirrored instance at 9 AM. Just a little after 9 AM someone deleted some data from a table because the WHERE clause was incorrect. Since we had a database snapshot, we could easily pull the data back and it saved us some time since we didn&#8217;t need to restore the database or recreate the data.

Now let&#8217;s take a look at some code to see what can be done after a bad delete

First create a test database

sql
USE master
GO
 
CREATE DATABASE [test] ON  PRIMARY
( NAME = N'test', FILENAME = N'C:test.mdf'  )
 LOG ON
( NAME = N'test_log', FILENAME = N'C:test_log.LDF' )
 
GO
```

Now create a table and populate it with some data

sql
USE test
GO
 
 
CREATE TABLE TestTable (id INT NOT NULL,somecol CHAR(100) DEFAULT 'a')
GO
 
INSERT TestTable(id)
SELECT ROW_NUMBER() OVER (ORDER BY s1.id)
FROM sysobjects s1
CROSS JOIN sysobjects s2
```

Now it is time to create your snapshot

sql
USE master
GO
 
   
CREATE DATABASE TestSnapshot ON  
( NAME = N'test', FILENAME = N'C:testss.mdf' )
  AS SNAPSHOT OF Test;
GO
```

If you run this, you will see that both the test database and the snapshot database table have the same number of rows

sql
SELECT COUNT(*) FROM TestSnapshot..TestTable
SELECT COUNT(*) FROM Test..TestTable
```

A database snapshot is read only and you cannot delete data, try it out.

sql
USE TestSnapshot
GO
 
DELETE TestTable
```

_Msg 3906, Level 16, State 1, Line 1
  
Failed to update database &#8220;TestSnapshot&#8221; because the database is read-only.
  
_ 
  
Now let&#8217;s delete all the data from the table in the test database

sql
USE Test
GO
 
DELETE  TestTable
```

Just to verify, grab the count from the table

sql
SELECT  COUNT(*)
FROM    TestTable
```

So the table is empty, but you can still get all the data back by inserting into the table from the database snapshot

sql
INSERT  TestTable
SELECT  *
FROM    TestSnapshot..TestTable
```

And now both tables have the same number of rows again

sql
SELECT  COUNT(*)
FROM    TestSnapshot..TestTable
SELECT  COUNT(*)
FROM    Test..TestTable
```

Just a couple of things to consider. If you have a lot of activity in your database then the database snapshot might grow considerably, be aware of that or you will run out of space if you placed the database snapshot on a drive that doesn&#8217;t have a lot of space.

If you data gets updated frequently then this won&#8217;t work either, the table in question for us was one where we had end of day values stored.

To drop the snapshot, you just use a regular DROP DATABASE command

sql
DROP DATABASE TestSnapshot 
```

To learn more about database snapshots, visit books on line: http://msdn.microsoft.com/en-us/library/ms175158.aspx

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22