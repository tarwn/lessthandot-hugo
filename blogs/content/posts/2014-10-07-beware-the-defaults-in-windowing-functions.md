---
title: Beware the defaults! (in windowing functions)
author: Koen Verbeeck
type: post
date: 2014-10-07T12:22:28+00:00
ID: 3007
url: /index.php/datamgmt/dbprogramming/mssqlserver/beware-the-defaults-in-windowing-functions/
views:
  - 6416
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - sql
  - syndicated
  - t-sql
  - windowing functions

---
<p style="text-align: justify">
  Some time ago I was writing some windowing functions on a set of data. Basically I was looking for the last date an event had occurred for each type of event. Let’s illustrate with an example:
</p>

sql
CREATE TABLE dbo.TestOver
	(ID INT IDENTITY(1,1) PRIMARY KEY NOT NULL
	,[Group] CHAR(1) NOT NULL
	,Value INT NOT NULL);

INSERT INTO dbo.TestOver([Group],Value)
VALUES	 ('A',1)
		,('A',2)
		,('A',3)
		,('A',4)
		,('B',5)
		,('B',6)
		,('B',7)
		,('B',8)
		,('B',9);
```<p style="text-align: justify">
  Using the data above, I need to find the value 4 for group A and the value 9 for group B. I first wrote the following T-SQL statement to retrieve the data:
</p>

sql
SELECT DISTINCT [Group], MAX(Value) OVER (PARTITION BY [Group] ORDER BY Value)
FROM dbo.TestOver;
```<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/10/query1.png"><img class="alignnone size-full wp-image-3013" src="/wp-content/uploads/2014/10/query1.png" alt="query1" width="595" height="290" srcset="/wp-content/uploads/2014/10/query1.png 595w, /wp-content/uploads/2014/10/query1-300x146.png 300w" sizes="(max-width: 595px) 100vw, 595px" /></a>
</p>

<p style="text-align: justify">
  The results are of course incorrect. A little baffled why this was the cause, I changed the ORDER BY to descending which gave me the results I wanted.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/10/query2.png"><img class="alignnone size-full wp-image-3011" src="/wp-content/uploads/2014/10/query2.png" alt="query2" width="617" height="142" srcset="/wp-content/uploads/2014/10/query2.png 617w, /wp-content/uploads/2014/10/query2-300x69.png 300w" sizes="(max-width: 617px) 100vw, 617px" /></a>
</p>

<p style="text-align: justify">
  I really didn’t think twice over this, until I joined the session <a href="http://www.sqlserverdays.be/powerful-t-sql-improvements-that-reduce-query-complexity/">Powerful T-SQL Improvements that Reduce Query Complexity</a> by Hugo Kornelis (<a href="http://sqlblog.com/blogs/hugo_kornelis/">blog</a> | <a href="https://twitter.com/Hugo_Kornelis">twitter</a>) on the SQL Server Days. I learned two things.
</p>

<ol style="text-align: justify">
  <li>
    You don’t need to specify the ORDER BY.
  </li>
</ol>

<p style="text-align: justify">
  In SQL Server 2005, the <a href="http://msdn.microsoft.com/en-us/library/ms189461(v=sql.90).aspx">OVER clause</a> was introduced and it simplified some aggregations like the one we’re doing here. When using the ranking window functions the ORDER BY clause is mandatory, but when using a regular aggregate window function the ORDER BY clause is not allowed. This gives us the following T-SQL which is the perfect solution for our problem here:
</p>

sql
SELECT DISTINCT [Group], MAX(Value) OVER (PARTITION BY [Group])
FROM dbo.TestOver;
```<p style="text-align: justify">
  To be honest, I completely forgot aggregate functions could be used this way. The PARTITION BY clause is optional as well, so you can have a completely empty OVER clause.
</p>

<ol style="text-align: justify" start="2">
  <li>
    When you do specify the ORDER BY, defaults come into play.
  </li>
</ol>

<p style="text-align: justify">
  Starting from SQL Server 2012, the T-SQL windowing functions and the <a href="http://msdn.microsoft.com/en-us/library/ms189461(v=sql.120).aspx">OVER clause</a> were greatly enhanced. Suddenly you can specify an ORDER BY for the aggregate windowing functions (which I did in the first attempts, remember?). However, if you specify an ORDER BY clause but no ROW or RANGE clause, SQL Server will apply the following defaults: RANGE UNBOUNDED PRECEDING as the lower limit and CURRENT ROW for the upper limit of the window. When Hugo explained this, I had my “Eureka” moment (or rather my “How could I have been this stupid?” moment). Because of these defaults, the MAX aggregate was calculated over the wrong windows! Let’s illustrate the concept for group A:
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/10/windows.png"><img class="alignnone size-full wp-image-3012" src="/wp-content/uploads/2014/10/windows.png" alt="windows" width="204" height="189" /></a>
</p>

<p style="text-align: justify">
  Because of the defaults, the first window is limited to only one row. This means the MAX aggregate will return the value 1. In the second window, two rows are included and MAX will return 2 and so on. By reversing the sort order, the value 4 will always be included in the windows, so MAX will return the correct answers. However dropping the ORDER BY is in my opinion the cleanest option to solve the problem.
</p>

<p style="text-align: justify">
  <strong>Conclusion</strong>
</p>

<p style="text-align: justify">
  Learn your T-SQL syntax and be aware of the defaults! Hugo also mentioned that ROWS <del>might</del> <strong>will</strong> have better performance than RANGE, so you better always specify your window frames to avoid the default.
</p>

<p style="text-align: justify">
  <i>Update: I was contacted by the amazing Rob Farley who told me that ROWS will beat RANGE any day of the week and that you should always specify ROWS unless you really need RANGE.</i>
</p>