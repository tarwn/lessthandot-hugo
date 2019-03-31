---
title: Listing all your SQL Server databases ordered by size
author: SQLDenis
type: post
date: 2013-02-08T11:51:00+00:00
excerpt: |
  To see all the databases with their size on an instance, you can use sp_helpdb. That works but returns the results in some random order. In my case I see master, model and
  msdb followed by a couple of user database, then tempdb and then again some user&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/listing-all-your-sql-server/
views:
  - 3315
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - databases
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - sqlcop

---
To see all the databases with their size on an instance, you can use sp_helpdb. That works but returns the results in some random order. In my case I see master, model and
  
msdb followed by a couple of user database, then tempdb and then again some user databases. What if I want to get the list returned order by size descending? This is pretty easy if you dump the results into a table and then do the sorting when doing the SELECT query against this table. The one thing you have to do is taking out MB from the db_size column and converting it to something numeric.

Here is what the query looks like

<pre>CREATE TABLE #test (name varchar(100), db_size varchar(100),owner varchar(100),db_id int,created varchar(100),status varchar(1000),compatibility_level int)
 
INSERT #test 
EXEC sp_helpdb
 
SELECT name,db_size,owner,db_id,created,compatibility_level 
FROM #test
ORDER BY CONVERT(float,REPLACE(db_size,' MB','')) DESC

DROP TABLE #test </pre>

And here is what you would see

<pre>name		     db_size	owner		db_id	created	    compatibility_level
TestBigger	    988.31 MB	DenisDenis	8	Nov 26 2012	110
TestSmaller	    710.31 MB	DenisDenis	9	Nov 26 2012	110
msdb		     38.00 MB	sa		4	Feb 10 2012	110
master		     18.63 MB	sa		1	Apr  8 2003	110
ReportServer	     12.94 MB	DenisDenis	5	Aug 16 2012	110</pre>

This &#8220;check&#8221; will be part of the informational section of SQLCop