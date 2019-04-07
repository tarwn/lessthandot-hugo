---
title: MongoDB vs. SQL Server – INSERT comparison part deux
author: SQLDenis
type: post
date: 2010-04-06T00:32:20+00:00
url: /index.php/datamgmt/dbprogramming/mongodb-vs-sql-server-insert-comparison/
views:
  - 19812
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - mongodb
  - nosql
  - performance

---
Lee Everest created a post named [MongoDB vs. SQL Server &#8211; INSERT comparison][1] where he compared inserting 50001 rows with MongoDB vs. SQL Server. So he claims that MongoDB inserts 50001 rows in 30 seconds while the SQL Server one takes 1.5 minutes. Okay so I looked at this SQL Script and can make 2 improvements

First create this table

<pre>CREATE TABLE MongoCompare
    (guid uniqueidentifier
    ,value int
    )
GO</pre>

Here is the script he used.

<pre>DECLARE @id int = 1
WHILE (@id < 500001)
BEGIN
    INSERT INTO MongoCompare VALUES (newid(), @id)
    SET @id+=1
END
GO</pre>

Now if I was to write that code I would write it with an explicit transaction and I would also use nocount on. So If we do this

<pre>SET NOCOUNT ON
BEGIN TRAN
DECLARE @id int = 1
WHILE (@id < 500001)
BEGIN
    INSERT INTO MongoCompare VALUES (newid(), @id)
    SET @id+=1
END
COMMIT</pre>

SQL Server now only takes 6 seconds and you have one atomic block of code, either all succeeds or nothing succeeds.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://texastoo.com/post/2010/04/04/MongoDB-vs-SQL-Server-INSERT-comparison.aspx
 [2]: http://forum.lessthandot.com/viewforum.php?f=17
 [3]: http://forum.lessthandot.com/viewforum.php?f=22