---
title: How to return only weekend data for the last 4 weeks in SQL Server
author: SQLDenis
type: post
date: 2010-08-06T15:00:40+00:00
url: /index.php/datamgmt/dbprogramming/how-to-return-only-weekend-data-for-the/
views:
  - 7571
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dates howto sql server 2008
  - sql server 2005

---
I answered [this question][1] today and thought it was interesting enough for a blog post.
  
The easiest way to return all rows that are not older than 4 weeks and fall on a wekeend would be to use that handy calendar table that you have.
  
Of course not everyone has a calendar table so here is a way to do it without.

First create this table

<pre>CREATE TABLE TestData (id INT NOT NULL PRIMARY KEY, 
			SomeData VARCHAR(36) NOT NULL, 
			SomeDate DATETIME NOT NULL)
GO</pre>

Now we have to insert some data, the query below will insert 2048 rows into the table

<pre>INSERT TestData
SELECT number, NEWID(), DATEADD(hh,- number,GETDATE())
FROM master..spt_values
WHERE TYPE = 'P'</pre>

The rows inserted will look something like this

<pre>0	286D95F8-FD33-4903-B3D2-644BC4AD88EE	2010-08-06 12:28:09.913
1	8C985938-085B-482F-A6C8-678954ADF3DA	2010-08-06 11:28:09.913
2	46437AF4-CF1C-4DF3-B9DA-36153B3844CD	2010-08-06 10:28:09.913
3	B5E15934-3F94-4543-9A6F-9C78BEEBB588	2010-08-06 09:28:09.913
4	9655A196-C434-4322-A9DC-14E676E45E25	2010-08-06 08:28:09.913
-----
-----
-----
2046	1710469B-0545-4719-95FF-B87570EC07C3	2010-05-13 06:28:09.913
2047	EF306E11-10F5-40C5-B2BC-862CAC5D5CBD	2010-05-13 05:28:09.913</pre>

So what we did is take the getdate() value and then subtract one hour from it for each row

Next up is creating an index on the SomeDate column

<pre>CREATE  INDEX ix_SomeDate ON TestData(SomeDate)
GO</pre>

Now we are ready to write our query. How do we grab all the data that is not older than 4 weeks?
  
We can use the following for that, if you are not on SQL Server 2008 or above use DATEADD(wk, DATEDIFF(wk, 0, GETDATE())-4, 0). If you are on SQL Server 2008 or above you can use CONVERT(DATE,DATEADD(WK,-4,GETDATE()))

If you run that today on August 8 2010 it returns 2010-07-09 00:00:00.000
  
Try it yourself

<pre>SELECT CONVERT(DATE,DATEADD(WK,-4,GETDATE()))

SELECT DATEADD(DD, DATEDIFF(dd, 0, DATEADD(WK,-4,GETDATE()))+0, 0)</pre>

Now we have to do the weekend part, in the US the start of the week is Sunday, if you check @@DATEFIRST it should return a 7

<pre>SELECT @@DATEFIRST</pre>

In Holland for example the week start on Saturday, run this to see what @@DATEFIRST returns for a different language

<pre>SET LANGUAGE Dutch;
GO
SELECT @@DATEFIRST;  --1
GO
SET LANGUAGE us_english;
GO
SELECT @@DATEFIRST;  --7</pre>

You can use DATEFIRST to change that, the command below will make the week start at 1

<pre>SET DATEFIRST 1;</pre>

So DATEPART(dw, Date) will return 7 for Sunday and 1 for Saturday in the US, in order to use this we need to add it like this in SQL&#8230; DATEPART(dw,SomeDate) IN(1,7). If you are not living where the week starts on a Sunday then modify the IN(1,7) part

Finally the query looks like this if you are not on SQL Server 2008 yet

<pre>SELECT * FROM TestData
WHERE SomeDate &gt;=DATEADD(DD, DATEDIFF(dd, 0, DATEADD(WK,-4,GETDATE()))+0, 0)
AND DATEPART(dw,SomeDate  ) IN(1,7)</pre>

If you are on SQl Server 2008, then you can use this query

<pre>SELECT * FROM TestData
WHERE SomeDate &gt;=CONVERT(DATE,DATEADD(WK,-4,GETDATE()))
AND DATEPART(dw,SomeDate  ) IN(1,7)</pre>

Below are the execution plans, as you can see both queries result in an index seek

<img src="/wp-content/uploads/blogs/DataMgmt//Execution plan.PNG" alt="" title="" width="855" height="536" />

Why the top query takes 60% and the bottom one 40% is a little strange to me, I know that they did some optimizations where CONVERT(DATE, Column) &#8230;. is now SARGAble but this should be executed only once

I will investigate that part further

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://stackoverflow.com/questions/3425412/sql-only-select-records-from-weekends/3425437#3425437
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22