---
title: Three Ways To Return All Rows That Contain Uppercase Characters Only
author: SQLDenis
type: post
date: 2008-06-24T16:26:50+00:00
url: /index.php/datamgmt/datadesign/three-ways-to-return-all-rows-that-conta/
views:
  - 3625
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
How do you select all the rows that contain uppercase characters only? There sre three ways to do this
  
1 Compare with BINARY_CHECKSUM
  
2 Use COLLATE
  
3 Cast to varbinary 

Let&#8217;s first create the table and also some test data 

CREATE TABLE #tmp ( x VARCHAR(10) NOT NULL ) 

<pre>INSERT INTO #tmp 
SELECT 'Word' UNION ALL 
SELECT 'WORD' UNION ALL 
SELECT 'ABC' UNION ALL 
SELECT 'AbC' UNION ALL 
SELECT 'ZxZ' UNION ALL 
SELECT 'ZZZ' UNION ALL 
SELECT 'word' </pre>

if we want only the uppercase columns then this is supposed to be our output 

WORD
  
ABC
  
ZZZ 

Let&#8217;s get started, first up is BINARY_CHECKSUM 

<pre>SELECT x 
FROM #TMP 
WHERE BINARY_CHECKSUM(x) = BINARY_CHECKSUM(UPPER(x)) </pre>

Second is COLLATE 

<pre>SELECT x 
FROM #TMP 
WHERE x = UPPER(x) COLLATE SQL_Latin1_General_CP1_CS_AS </pre>

Third is Cast to varbinary 

<pre>SELECT x 
FROM #TMP 
WHERE CAST(x AS VARBINARY(10)) = CAST(UPPER(x) AS VARBINARY(10)) </pre>

Of course if you database is already case sensitive you can just do the following 

<pre>SELECT x 
FROM #TMP 
WHERE UPPER(x) = x </pre>

That will work, how do you find out what collation was used when your database was created? You can use DATABASEPROPERTYEX for that. I use the model DB here because when you create a new DB by default it inherits all the properties from the model DB.
  
When I run this 

<pre>SELECT DATABASEPROPERTYEX( 'model' , 'collation' ) </pre>

I get this as output: SQL\_Latin1\_General\_CP1\_CI_AS 

What does all that junk mean? Well let&#8217;s run the following function (yes those are 2 colons :: )

<pre>SELECT * 
FROM ::fn_helpcollations () 
WHERE NAME ='SQL_Latin1_General_CP1_CI_AS' </pre>

The description column contains this info 

Latin1-General, case-insensitive, accent-sensitive,
  
kanatype-insensitive, width-insensitive for Unicode Data,
  
SQL Server Sort Order 52 on Code Page 1252 for non-Unicode Data 

You can read some more info about Selecting a SQL Collation here: http://msdn2.microsoft.com/en-us/library/aa176552(SQL.80).aspx