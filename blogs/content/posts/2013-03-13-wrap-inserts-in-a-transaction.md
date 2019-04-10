---
title: Wrap inserts in a transaction for faster performance
author: SQLDenis
type: post
date: 2013-03-13T13:54:00+00:00
ID: 2032
excerpt: "Sometimes you have to insert a bunch of data and you can't use BCP or another bulk load method. When you do single row inserts, SQL Server wraps these inserts inside an implicit transaction. Did you know that if you use an explicit transaction that the&hellip;"
url: /index.php/datamgmt/dbprogramming/wrap-inserts-in-a-transaction/
views:
  - 32069
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - performance
  - sql server 2008
  - sql server 2012
  - testing
  - transactions

---
Sometimes you have to insert a bunch of data and you can't use BCP or another bulk load method. When you do single row inserts, SQL Server wraps these inserts inside an implicit transaction. Did you know that if you use an explicit transaction that the inserts will be much faster? I touched upon this earlier in this post [MongoDB vs. SQL Server – INSERT comparison part deux][1] but since someone asked about this today, I decided to take another look with different run sizes as well

Let's take a look. first create the following table

sql
CREATE TABLE Sometest(id INT PRIMARY KEY, SomeCol VARCHAR(200), SomeDate DATETIME,SomeCol2 VARCHAR(200), SomeDate2 DATETIME,
SomeCol3 VARCHAR(200), SomeDate3 DATETIME,SomeCol4 VARCHAR(200), SomeDate4 DATETIME)
GO
```

Now run the following code

sql
TRUNCATE TABLE Sometest
DECLARE @start DATETIME = GETDATE()
SET NOCOUNT ON
--BEGIN TRAN
DECLARE @id INT =0
WHILE @id < 50000
BEGIN
	INSERT Sometest
	SELECT @id ,'BlaBlaBlaBlaBlaBlaBlaBlaBlaBla111111',GETDATE(),
	'BlaBlaBlaBlaBlaBlaBlaBlaBlaBla2222',GETDATE(),
	'BlaBlaBlaBlaBlaBlaBlaBlaBlaBla3333',GETDATE(),
	'BlaBlaBlaBlaBlaBlaBlaBlaBlaBla4444',GETDATE()
SET @id+=1
END
--COMMIT
SELECT DATEDIFF(ms,@start,GETDATE())
SELECT COUNT(*) FROM Sometest
```

That takes 1153 milliseconds on my machine

Run the same code but now uncomment the transaction

sql
TRUNCATE TABLE Sometest
DECLARE @start DATETIME = GETDATE()
SET NOCOUNT ON
BEGIN TRAN
DECLARE @id INT =0
WHILE @id < 50000
BEGIN
	INSERT Sometest
	SELECT @id ,'BlaBlaBlaBlaBlaBlaBlaBlaBlaBla111111',GETDATE(),
	'BlaBlaBlaBlaBlaBlaBlaBlaBlaBla2222',GETDATE(),
	'BlaBlaBlaBlaBlaBlaBlaBlaBlaBla3333',GETDATE(),
	'BlaBlaBlaBlaBlaBlaBlaBlaBlaBla4444',GETDATE()
SET @id+=1
END
COMMIT
SELECT DATEDIFF(ms,@start,GETDATE())
SELECT COUNT(*) FROM Sometest
```

That is almost twice as fast (or almost half as slow), it takes 673 milliseconds

Here is what the numbers look like on my machine for different insert sizes

<pre>Inserts	no tran	explicit transaction
100000  18030   10800
50000	 9363	 5516
5000	 1130	  760
5000	 190	  103
1000	  40       23</pre>

As you can see, when you have an explicit transaction it is much faster than when you don't specify a transaction

 [1]: /index.php/DataMgmt/DBProgramming/mongodb-vs-sql-server-insert-comparison