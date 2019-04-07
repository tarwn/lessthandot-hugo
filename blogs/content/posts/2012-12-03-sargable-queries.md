---
title: 'SQL Advent 2012 Day 3: Sargable Queries'
author: SQLDenis
type: post
date: 2012-12-03T13:44:00+00:00
excerpt: 'This is day three of the SQL Advent 2012 series of blog posts. Today we are going to look at sargable queries. You might ask yourself, what is this weird term sargable. Sargable  comes from searchable argument, sometimes also referred as Search ARGument&hellip;'
url: /index.php/datamgmt/dbprogramming/sargable-queries/
views:
  - 16180
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database
  - indexing
  - performance tuning
  - rdbms
  - sargable
  - sql
  - sql server 2008
  - sql server 2012
  - t-sql

---
This is day three of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at sargable queries. You might ask yourself, what is this weird term sargable. Sargable comes from searchable argument, sometimes also referred as <span class="MT_red">S</span>earch <span class="MT_red">ARG</span>ument <span class="MT_red">ABLE</span>. What that means is that the query will be able to use an index, a seek will be performed instead of a scan. In general any time you have a function wrapped around a column, an index won&#8217;t be used

Some examples that are not sargable 

<pre>WHERE LEFT(Name,1) = 'S'
WHERE Year(SomeDate) = 2012
WHERE OrderID * 3 = 33000</pre>

Those three should be rewritten like this in order to become sargable 

<pre>WHERE Name LIKE 'S%'
WHERE SomeDate &gt;= '20120101' AND SomeDate < '20130101'
WHERE OrderID = 33000/3</pre>

Let&#8217;s create a table, insert some data so that we can look at the execution plan
  
Create this simple table

<pre>CREATE TABLE Test(SomeID varchar(100))</pre>

Let&#8217;s insert some data that will start with a letter followed by some digits

<pre>INSERT Test
SELECT LEFT(v2.type,1) +RIGHT('0000' + CONVERT(varchar(4),v1.number),4) 
FROM master..spt_values v1
CROSS JOIN (SELECT DISTINCT LEFT(type,1) AS type 
FROM master..spt_values) v2
WHERE v1.type = 'p'</pre>

That insert should have generated 32768 rows

Now create this index on that table

<pre>CREATE CLUSTERED INDEX cx_test ON Test(SomeID)</pre>

Let&#8217;s take a look at the execution plan, hit CTRL + M, this will add the execution plan once the query is done running

<pre>SELECT * FROM Test
WHERE SomeID LIKE 's%'

SELECT * FROM Test
WHERE LEFT(SomeID,1) = 's'</pre>

Here is what the plans looks like

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Excecutionplan.PNG?mtime=1354498760"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Excecutionplan.PNG?mtime=1354498760" width="447" height="295" /></a>
</div>

As you can see it is 9% versus 91% between the two queries, that is a big difference
  
Hit CTRL + M again to disable the inclusion of the plan

Run this codeblock, it will give you the plans in a text format

<pre>SET SHOWPLAN_TEXT ON
GO

SELECT * FROM Test
WHERE SomeID LIKE 's%'

SELECT * FROM Test
WHERE LEFT(SomeID,1) = 's'
GO

SET SHOWPLAN_TEXT OFF
GO</pre>

Here are the two plans

> |&#8211;Clustered Index Seek(OBJECT:([master].[dbo].[Test].[cx_test]),
    
> SEEK:([master].[dbo].[Test].[SomeID] >= &#8216;Rþ&#8217; AND [master].[dbo].[Test].[SomeID] < 'T'), WHERE:([master].[dbo].[Test].[SomeID] like 's%') ORDERED FORWARD) |--Clustered Index Scan(OBJECT:([master].[dbo].[Test].[cx_test]), WHERE:(substring([master].[dbo].[Test].[SomeID],(1),(1))='s')) 

As you can see the top one while looking more complicated is actually giving you a seek

## Making a case sensitive search sargable

Now let&#8217;s take a look at how we can make a case sensitive search sargable as well
  
In order to do a search and make it case sensitive, you have to have a case sensitive collation, if your table is not created with a case sensitive collation then you can supply it as part of the query

Here is an example to demonstrate what I mean

This is a simple table created without a collation

<pre>CREATE TABLE TempCase1 (Val CHAR(1))
INSERT TempCase1 VALUES('A')
INSERT TempCase1 VALUES('a')</pre>

Running this select statement will return both rows 

<pre>SELECT * FROM TempCase1
WHERE Val = 'A' </pre>

Val
  
&#8212;&#8211;
  
A
  
a

Now create the same kind of table but with a case sensitive collation

<pre>CREATE TABLE TempCase2 (Val CHAR(1) COLLATE SQL_Latin1_General_CP1_CS_AS)
INSERT TempCase2 VALUES('A')
INSERT TempCase2 VALUES('a')</pre>

Run the same query

<pre>SELECT * FROM TempCase2
WHERE Val = 'A' </pre>

Val
  
&#8212;&#8211;
  
A

As you can see you only get the one row now that matches the case

<pre>SELECT * FROM TempCase1
WHERE Val = 'A' COLLATE SQL_Latin1_General_CP1_CS_AS</pre>

Val
  
&#8212;&#8211;
  
A
  
a

Now let&#8217;s take a look at how we can make the case sensitive search sargable

First create this table and insert some data

<pre>CREATE TABLE TempCase (Val CHAR(1))
 
INSERT TempCase VALUES('A')
INSERT TempCase VALUES('B')
INSERT TempCase VALUES('C')
INSERT TempCase VALUES('D')
INSERT TempCase VALUES('E')
INSERT TempCase VALUES('F')
INSERT TempCase VALUES('G')
INSERT TempCase VALUES('H')</pre>

Now we will insert some lowercase characters

<pre>INSERT TempCase
SELECT LOWER(Val) FROM TempCase</pre>

Now we will create our real table which will have 65536 rows

<pre>CREATE TABLE CaseSensitiveSearch (Val VARCHAR(50))</pre>

We will do a couple of cross joins to generate the data for our queries

<pre>INSERT CaseSensitiveSearch
SELECT t1.val + t2.val + t3.val + t4.val
FROM TempCase t1
CROSS JOIN TempCase t2
CROSS JOIN TempCase t3
CROSS JOIN TempCase t4</pre>

Create an index on the table

<pre>CREATE INDEX IX_SearchVal ON CaseSensitiveSearch(Val)</pre>

Just like before, if we run this we will get back the exact value we passed in and also all the upper case and lower case variations

<pre>SELECT * FROM CaseSensitiveSearch
WHERE Val = 'ABCD' </pre>

Here are the results of that query
  
Val
  
&#8212;&#8211;
  
AbCd
  
ABcd
  
Abcd
  
ABCd
  
aBCd
  
abCd
  
aBcd
  
abcd
  
abCD
  
aBcD
  
abcD
  
aBCD
  
ABCD
  
AbCD
  
ABcD
  
AbcD

If you add the collation to the query, you will get only what matches your value

<pre>SELECT * FROM CaseSensitiveSearch
WHERE Val = 'ABCD' COLLATE SQL_Latin1_General_CP1_CS_AS</pre>

Here is the result, it maches what was passed in
  
Val
  
&#8212;
  
ABCD

The problem with the query above is that it will cause a scan. So what can we do, how can we make it perform better? It is simple combine the two queries
  
First grab all case sensitive and case insensitive values and then after that filter out the case insensitive values

Here is what that query will look like

<pre>SELECT * FROM CaseSensitiveSearch
WHERE Val = 'ABCD' COLLATE SQL_Latin1_General_CP1_CS_AS
AND Val LIKE 'ABCD'</pre>

AND Val LIKE &#8216;ABCD&#8217; will result in a seek, now when it also does the Val = &#8216;ABCD&#8217; COLLATE SQL\_Latin1\_General\_CP1\_CS_AS part, it only returns the row that matches your value

If you run both queries, you can look at the plan difference (hit CTRL + M so that the plan is included)

<pre>SELECT * FROM CaseSensitiveSearch
WHERE Val = 'ABCD' COLLATE SQL_Latin1_General_CP1_CS_AS



SELECT * FROM CaseSensitiveSearch
WHERE Val = 'ABCD' COLLATE SQL_Latin1_General_CP1_CS_AS
AND Val LIKE 'ABCD'</pre>

Here is the plan

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ExcecutionPlan2008.PNG?mtime=1354548699"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ExcecutionPlan2008.PNG?mtime=1354548699" width="903" height="364" /></a>
</div>

As you can see, there is a big difference between the two

Here is the plan in text as well

<pre>SET SHOWPLAN_TEXT ON
GO
 
SELECT * FROM CaseSensitiveSearch
WHERE Val = 'ABCD' COLLATE SQL_Latin1_General_CP1_CS_AS



SELECT * FROM CaseSensitiveSearch
WHERE Val = 'ABCD' COLLATE SQL_Latin1_General_CP1_CS_AS
AND Val LIKE 'ABCD'

GO
 
SET SHOWPLAN_TEXT OFF
GO</pre>

> |&#8211;Table Scan(OBJECT:([tempdb].[dbo].[CaseSensitiveSearch]),
     
> WHERE:(CONVERT_IMPLICIT(varchar(50),[tempdb].[dbo].[CaseSensitiveSearch].[Val],0)=CONVERT(varchar(8000),[@1],0)))
> 
> |&#8211;Index Seek(OBJECT:([tempdb].[dbo].[CaseSensitiveSearch].[IX_SearchVal]), SEEK:([tempdb].[dbo].[CaseSensitiveSearch].[Val] >= &#8216;ABCD&#8217;
       
> AND [tempdb].[dbo].[CaseSensitiveSearch].[Val] <= 'ABCD'), WHERE:(CONVERT_IMPLICIT(varchar(50),[tempdb].[dbo].[CaseSensitiveSearch].[Val],0)='ABCD' AND [tempdb].[dbo].[CaseSensitiveSearch].[Val] like 'ABCD') ORDERED FORWARD)

Also take a look at [Only In A Database Can You Get 1000% + Improvement By Changing A Few Lines Of Code][2] to see how this works with dates

That is all for day three of the SQL Advent 2012 series, come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][3]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/DataDesign/only-in-a-database-can-you-get-1000-impr
 [3]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap