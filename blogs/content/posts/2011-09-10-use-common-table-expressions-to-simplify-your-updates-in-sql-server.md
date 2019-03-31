---
title: Use common table expressions to simplify your updates in SQL Server
author: SQLDenis
type: post
date: 2011-09-10T08:40:00+00:00
excerpt: |
  Someone asked two interesting questions last night
  
  1) Is there a way to construct an empty CTE?
  2) Can a CTE be modified? Can I insert rows after it has been constructed?
  
  Let's look at question number one first. Is there a way to construct an emp&hellip;
url: /index.php/datamgmt/datadesign/use-common-table-expressions-to-simplify-your-updates-in-sql-server/
views:
  - 25859
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - common table expression
  - cte
  - data manipulation
  - sql server
  - updates

---
Someone asked [two interesting questions][1] last night

1) Is there a way to construct an empty CTE?
  
2) Can a CTE be modified? Can I insert rows after it has been constructed?

Let&#8217;s look at question number one first. Is there a way to construct an empty CTE? Yes, there is, but why would you want to do this? Anyway, here is one way of having an empty CTE

<pre>WITH Hello 
AS (
SELECT  name
FROM sysobjects WHERE 1 =0
)
SELECT * FROM Hello</pre>

Question number two (Can a CTE be modified? Can I insert rows after it has been constructed?) is more interesting, first let&#8217;s look at a simple example and then we will look at something that makes more sense

We need to create a table first with 1 row

<pre>CREATE TABLE testNow(id int)
INSERT testNow VALUES(1)</pre>

Now run the following which will run an update statement against the CTE

<pre>;WITH Hello 
AS (
SELECT id FROM testNow

)

UPDATE Hello SET id = 2</pre>

If we check the table now, we can see that the value changed from 1 to 2

<pre>SELECT * FROM testNow</pre>

What about inserts, can we insert into a common table expression? Sure, let&#8217;s take a look how we can do that.

<pre>;WITH Hello 
AS (
SELECT id FROM testNow

)

INSERT Hello VALUES( 3 )</pre>

If we check the table now, we will see two rows in the table

<pre>SELECT * FROM testNow</pre>

So this is all, but what is the point? Really for the examples I gave there is none, you can just update the table directly instead. But, let&#8217;s look at a more interesting example

First we need to create the following table

<pre>CREATE TABLE SomeTable(id int,SomeVal char(1),SomeDate date)
INSERT SomeTable 
SELECT null,'A','20110101'
UNION ALL
SELECT null,'A','20100101'
UNION ALL
SELECT null,'A','20090101'
UNION ALL
SELECT null,'B','20110101'
UNION ALL
SELECT null,'B','20100101'
UNION ALL
SELECT null,'C','20110101'
UNION ALL
SELECT null,'C','20100101'
UNION ALL
SELECT null,'C','20090101'</pre>

Now run a simple select statement

<pre>SELECT * FROM SomeTable</pre>

Here is what is in that table

<pre>id	SomeVal	SomeDate
NULL	A	2011-01-01
NULL	A	2010-01-01
NULL	A	2009-01-01
NULL	B	2011-01-01
NULL	B	2010-01-01
NULL	C	2011-01-01
NULL	C	2010-01-01
NULL	C	2009-01-01</pre>

Now I would like to set the id to 1 for each distinct SomeVal where the date is the max date and to 0 otherwise
  
So my table would look like this

<pre>id	SomeVal	SomeDate
1	A	2011-01-01
0	A	2010-01-01
0	A	2009-01-01
1	B	2011-01-01
0	B	2010-01-01
1	C	2011-01-01
0	C	2010-01-01
0	C	2009-01-01</pre>

First let&#8217;s do the select statement, in order to accomplish what we set out to do we can use the row_number function, partition by SomeVal and order by SomeDate descending

Run the following statement

<pre>;WITH cte AS(SELECT 
		ROW_NUMBER() OVER(PARTITION BY SomeVal 
				  ORDER BY SomeDate DESC) AS row,
				* FROM SomeTable)
SELECT * FROM cte</pre>

Here is our result

<pre>row	id	SomeVal	SomeDate
1	NULL	A	2011-01-01
2	NULL	A	2010-01-01
3	NULL	A	2009-01-01
1	NULL	B	2011-01-01
2	NULL	B	2010-01-01
1	NULL	C	2011-01-01
2	NULL	C	2010-01-01
3	NULL	C	2009-01-01</pre>

So where row is 1 we will make SomeVal 1 otherwise we will make it 0, we can do that with a simple case statement

Here is what the update looks like

<pre>;WITH cte AS(SELECT 
		ROW_NUMBER() OVER(PARTITION BY SomeVal 
				  ORDER BY SomeDate DESC) AS row,
				* FROM SomeTable)

UPDATE cte
SET id =  CASE WHEN row = 1 THEN 1 ELSE 0 END</pre>

Now run the select against the table again

<pre>SELECT * FROM SomeTable</pre>



<pre>id	SomeVal	SomeDate
1	A	2011-01-01
0	A	2010-01-01
0	A	2009-01-01
1	B	2011-01-01
0	B	2010-01-01
1	C	2011-01-01
0	C	2010-01-01
0	C	2009-01-01</pre>

And as you can see the id column was updated correctly.

As you can see, using a common table expression can simplify your update statements.

 [1]: http://stackoverflow.com/questions/7369062/three-cte-questions