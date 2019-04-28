---
title: Full outer join requires some thinking before use
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-10T06:03:00+00:00
ID: 1750
excerpt: "I don't need Full outer joins all that often. So when I do I have to think on what it will return me."
url: /index.php/datamgmt/dbprogramming/mssqlserver/full-outer-join-requires-some/
views:
  - 4149
categories:
  - Microsoft SQL Server
tags:
  - full outer join
  - sqlserver

---
I don&#8217;t need Full outer joins all that often. So when I do I have to think on what it will return me. 

You can go to [wikipedia][1] to find the full explanation on full outer joins if you want to.

But in short it is this.

> Conceptually, a full outer join combines the effect of applying both left and right outer joins. Where records in the FULL OUTER JOINed tables do not match, the result set will have NULL values for every column of the table that lacks a matching row. For those records that do match, a single row will be produced in the result set (containing fields populated from both tables).
  
> For example, this allows us to see each employee who is in a department and each department that has an employee, but also see each employee who is not part of a department and each department which doesn&#8217;t have an employee.

What I needed was to combine the result of two queries where I did a sum by year and join on the year. 

Here is the testdate.

```tsql
CREATE TABLE #temp 
(
	Id INT IDENTITY(1,1) PRIMARY KEY NOT NULL
	, DateIn DATETIME NULL
	, DateOut DATETIME NULL
)
GO

INSERT INTO #temp (DateIn, DateOut) VALUES ('2012-10-10 9:38:45.757',null) 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2011-10-10 9:38:45.757','2012-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2010-10-10 9:38:45.757','2011-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2010-10-10 9:38:45.757','2011-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2012-10-10 9:38:45.757',null) 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2011-10-10 9:38:45.757','2012-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2009-10-10 9:38:45.757','2010-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2009-10-10 9:38:45.757','2010-10-10 9:38:45.757') 
GO

SELECT Id, datein, dateout FROM #temp
GO

DROP TABLE #temp
GO```
And the desired result was something like this.

<div class="tables">
  <table>
    <tr>
      <th>
        Year
      </th>
      
      <th>
        NumberIn
      </th>
      
      <th>
        NumberOut
      </th>
    </tr>
    
    <tr>
      <td>
        2009
      </td>
      
      <td>
        2
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        2010
      </td>
      
      <td>
        2
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        2011
      </td>
      
      <td>
        2
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        2012
      </td>
      
      <td>
        2
      </td>
      
      <td>
        2
      </td>
    </tr>
  </table>
</div>

My first query was this.

```tsql
SELECT DateInData.YEAR, COALESCE(numberin,0) AS numberin, COALESCE(numberout,0) AS numberout
FROM
	(SELECT YEAR(datein) AS YEAR, COUNT(*) AS numberin FROM #temp GROUP BY YEAR(datein) HAVING YEAR(datein) IS NOT NULL) AS DateInData
	FULL OUTER JOIN 
	(SELECT YEAR(dateout) AS YEAR, COUNT(*) AS numberout FROM #temp GROUP BY YEAR(dateout) HAVING YEAR(dateout) IS NOT NULL) AS DateOutData
	ON DateInData.[YEAR] = DateOutData.[YEAR]
GO```
And hooray, that gives the correct results. So lets move on.

Hold on. In the above I am assuming that datein will always be filled in. But my table definition says it is not a mandatory field.

So what would happen if I had the following data in my table.

```tsql
INSERT INTO #temp (DateIn, DateOut) VALUES ('2012-10-10 9:38:45.757',null) 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2011-10-10 9:38:45.757','2012-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES (null,'2011-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES (null,'2011-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2012-10-10 9:38:45.757',null) 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2011-10-10 9:38:45.757','2012-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2009-10-10 9:38:45.757','2010-10-10 9:38:45.757') 
INSERT INTO #temp (DateIn, DateOut) VALUES ('2009-10-10 9:38:45.757','2010-10-10 9:38:45.757') 
GO```
Then the result would look like the below.

<div class="tables">
  <table>
    <tr>
      <th>
        Year
      </th>
      
      <th>
        NumberIn
      </th>
      
      <th>
        NumberOut
      </th>
    </tr>
    
    <tr>
      <td>
        2009
      </td>
      
      <td>
        2
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        NULL
      </td>
      
      <td>
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        2011
      </td>
      
      <td>
        2
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        2012
      </td>
      
      <td>
        2
      </td>
      
      <td>
        2
      </td>
    </tr>
  </table>
</div>

See how we are missing a year? The user won&#8217;t be happy if he has to guess the year.

The solution is simple.

```tsql
SELECT coalesce(DateInData.YEAR, DateOutData.Year), COALESCE(numberin,0) AS numberin, COALESCE(numberout,0) AS numberout
FROM
	(SELECT YEAR(datein) AS YEAR, COUNT(*) AS numberin FROM #temp GROUP BY YEAR(datein) HAVING YEAR(datein) IS NOT NULL) AS DateInData
	FULL OUTER JOIN 
	(SELECT YEAR(dateout) AS YEAR, COUNT(*) AS numberout FROM #temp GROUP BY YEAR(dateout) HAVING YEAR(dateout) IS NOT NULL) AS DateOutData
	ON DateInData.[YEAR] = DateOutData.[YEAR]
GO```
I added the coalesce(dateindata.year, dateoutdata.year) in other words if the year of dateindata is null then go for the year that is in dateoutdata.

And now the user no longer has to guess the year.

<div class="tables">
  <table>
    <tr>
      <th>
        Year
      </th>
      
      <th>
        NumberIn
      </th>
      
      <th>
        NumberOut
      </th>
    </tr>
    
    <tr>
      <td>
        2009
      </td>
      
      <td>
        2
      </td>
      
      <td>
      </td>
    </tr>
    
    <tr>
      <td>
        2010
      </td>
      
      <td>
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        2011
      </td>
      
      <td>
        2
      </td>
      
      <td>
        2
      </td>
    </tr>
    
    <tr>
      <td>
        2012
      </td>
      
      <td>
        2
      </td>
      
      <td>
        2
      </td>
    </tr>
  </table>
</div>

So sometimes you just have to look at your table definition before making assumptions because your assumptions might be wrong, one would think that there is always a datein when there is a dateout but one can not be sure.

 [1]: http://en.wikipedia.org/wiki/Join_(SQL)#Full_outer_join