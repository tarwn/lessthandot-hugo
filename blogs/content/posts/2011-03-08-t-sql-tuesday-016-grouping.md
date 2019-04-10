---
title: 'T-SQL Tuesday #016 Grouping Market Data With T-SQL'
author: SQLDenis
type: post
date: 2011-03-08T10:52:00+00:00
ID: 1068
excerpt: "This month's T-SQL Tuesday is hosted by Jes Borland and it is all about grouping and aggregate functions, here is my attempt. I wrote most of this post on my way to the MVP summit in Seattle. This post is all about the stock market, charting data for In&hellip;"
url: /index.php/datamgmt/datadesign/t-sql-tuesday-016-grouping/
views:
  - 7937
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - grouping
  - ranking
  - t-sql tuesday

---
[![][1]][2]

This month's T-SQL Tuesday is hosted by [Jes Borland][3] and it is all about grouping and aggregate functions, here is my attempt. I wrote most of this post on my way to the MVP summit in Seattle. This post is all about the stock market, charting data for Intraday chart and for end of day charts. This post contains a lot of code and I apologize for that, I just want you to be able to run the code, in order to do that we have to setup all the tables and data.

Most people think of aggregate/grouping functions being Min, Max, Avg, Sum etc etc. To me Row_number and other ranking/windowing functions, datepart, converting to date, year() and month() can also be considered grouping
  

  
When I say _intraday_, I mean the values that you would see for a stock if you would pull up a chart at 3 PM, in this case it would be a chart from 9:30 AM till 4PM if this was traded on for example the NYSE or NASDAQ.

_End of day values_ are close values, so for example the close price for Apple on March 4 was $360.

We are going to chart intraday in either 1 day or 5 day charts. If it is one day we will chart in minutes, if it is 5 days we will chart in 5 minute increments.
  
For end of day data we are going to chart daily if it is 1 month, 3 months or 6 months and weekly if it is greater than 6 months.

So to start we are going to make up some tables with some fake data.

### Preparing the tables and data

First we need a table of symbols (some people will call them tickers). This table is very simple, all it has is a SymbolID and a Symbol. In reality this table would look different because sometimes companies will change the symbol, when Sun Microsystems changed from SUNW to JAVA is one such example.
  
Here is the table

sql
CREATE TABLE Symbols (
       SymbolID INT NOT NULL PRIMARY KEY,
       Symbol VARCHAR(20) NOT NULL)
```

We will insert these 4 symbols

sql
INSERT Symbols VALUES(1,'ABC')
INSERT Symbols VALUES(2,'DEF')
INSERT Symbols VALUES(3,'MNO')
INSERT Symbols VALUES(4,'XYZ')
```

Next up is the creation of the table of numbers, this will facilitate the creation of the data later on.

sql
CREATE TABLE Numbers (number INT NOT NULL  PRIMARY KEY)
GO
```

This will populate the table with 90000 rows.

sql
INSERT Numbers
SELECT TOP 90000 ROW_NUMBER() OVER(ORDER BY s1.id )
FROM sysobjects s1,sysobjects s2,sysobjects s3
```

Next up is a table that will hold some time information

sql
CREATE TABLE TempTickTime ( TickTime DATETIME NOT NULL)
GO
```

This will populate that table with 30 second intervals between 2011-02-28 09:30:30.000 and 2011-03-31 15:30:00.000 only when it is between 9:30 AM and 4 PM

sql
DECLARE @StartTime DATETIME = '20110228 09:30:00'
INSERT TempTickTime
SELECT DATEADD(s,number * 30,@StartTime)
FROM Numbers
WHERE  CONVERT(TIME, DATEADD(s,number * 30,@StartTime)) BETWEEN '09:30:00.0000000' AND '16:00:00.0000000'
```

Now when you deal with global markets, some instruments trade Monday through Friday, some of them trade Sunday till Thursday and there are other variations.
  
From the 4 tickers we have, two will trade Monday through Friday and two will trade Sunday till Thursday

Create this table and populate it as follows

sql
CREATE TABLE TickData (
       SymbolID INT NOT NULL,
       TickTime DATETIME NOT NULL,
       TickPrice DECIMAL (20,10) NOT NULL)
 

SET DATEFIRST 1 --Default to Sunday as 1
 
INSERT TickData
SELECT SymbolId,tickTime,1100 + 1 * RAND() * CONVERT(FLOAT,tickTime) * SymbolId/2.01
FROM TempTickTime t
CROSS JOIN Symbols s
WHERE DATEPART(dw,tickTime) BETWEEN 2 AND 6 --Monday till Friday
AND s.SymbolID IN (1,2)
ORDER BY tickTime
 

INSERT TickData
SELECT SymbolId,tickTime,1100 + 1 * RAND() * CONVERT(FLOAT,tickTime) * SymbolId/2.01
FROM TempTickTime t
CROSS JOIN Symbols s
WHERE DATEPART(dw,tickTime) BETWEEN 1 AND 5 --Sunday till Thursday
AND s.SymbolID IN (3,4)
ORDER BY tickTime
```

What the query does is insert the SymbolID, the ticktime and then a pseudo random value that represents the price. The query also is grouping by day of week by using the DATEPART function.

We are done with intraday data, next up is end of day

First create this table

sql
CREATE TABLE EODData (
       SymbolID INT NOT NULL,
       SomeDate DATETIME NOT NULL,
       TickPrice DECIMAL (20,10) NOT NULL,
       IsEndOfWeek tinyint NOT NULL)
```

In the query below we are grabbing the max time per day for a SymbolID and the associated price for that time. We are in essence grouping by SymbolId and Date, since we are ordering by TickTime descending and we are only grabbing where the ROW value is 1, we will get the latest value for a day.

sql
;WITH CTE AS(SELECT *,
ROW_NUMBER() OVER (PARTITION BY SymbolId,CONVERT(DATE,TickTime) ORDER BY TickTime DESC) AS ROW
FROM TickData)
 
INSERT EODData
SELECT SymbolId,CONVERT(DATE,TickTime),TickPrice,0 FROM CTE
WHERE ROW = 1
ORDER BY SymbolID,CONVERT(DATE,TickTime)
```

Here is another way of doing the insert by grouping by SymbolId and converting the TickTime to a date and grabbing the max TickTime for that, with this derived table we join back to the TickData table and do our inserts.

sql
--INSERT EODData
SELECT t.SymbolId,CONVERT(DATE,TickTime),TickPrice,0
FROM TickData t
JOIN(
       SELECT SymbolId,MAX(TickTime) as MaxTime
       FROM TickData
       GROUP BY SymbolId,CONVERT(DATE,TickTime)) x on
t.SymbolId = x.SymbolId
and t.TickTime =  x.MaxTime
```

Here is where we do some grouping, in order to grab the last possible value for a week, we need to group by SymbolID, year, month and the week number.

sql
SELECT SymbolId,MAX(SomeDate) as MaxDate
FROM EODData
GROUP BY SymbolId,YEAR(SomeDate), MONTH(SomeDate),DATEPART(wk,SomeDate)
order by SymbolID, MaxDate
```

That query produces the following output, as you can see it has the latest value for each week for each symbol.
  


<div class="tables">
  <table>
    <tr>
      <th>
        SymbolId
      </th>
      
      <th>
        MaxDate
      </th>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2011-03-05 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2011-03-12 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2011-03-19 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2011-03-26 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2011-03-31 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2011-03-05 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2011-03-12 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2011-03-19 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2011-03-26 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2011-03-31 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2011-02-28 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2011-03-04 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2011-03-11 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2011-03-18 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2011-03-25 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2011-03-31 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        2011-02-28 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        2011-03-04 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        2011-03-11 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        2011-03-18 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        2011-03-25 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        2011-03-31 00:00:00.000
      </td>
    </tr>
  </table>
</div>

Here is an example of how to join the grouping query to the table so that we can get all the details for the row, we will use this as the basis for our update later on

sql
SELECT e.* from EODData e
join (
       SELECT SymbolId,MAX(SomeDate) as MaxDate
       FROM EODData
       GROUP BY SymbolId,YEAR(SomeDate), MONTH(SomeDate),DATEPART(wk,SomeDate)) x
on e.SomeDAte = x.MaxDate
and e.SymbolId =x.SymbolId
```

And here is how we update the IsEndOfWeek column with the value 1 for the rows that fall on the end of the week

sql
UPDATE  e
SET e.IsEndOfWeek = 1
FROM EODData e
JOIN (SELECT SymbolId,MAX(SomeDate) AS MaxDate
       FROM EODData
       GROUP BY SymbolId,YEAR(SomeDate), MONTH(SomeDate),DATEPART(wk,SomeDate)
       ) x
ON e.SomeDate = x.MaxDate
AND e.SymbolId =x.SymbolId
```

### Charting end of day values

If we chart 1,3 or 6 months we will use daily values

sql
SELECT *
FROM EODData
WHERE SymbolId = 1
ORDER BY SomeDate
```

If we chart anything over 6 months we want to grab weekly values, the query for that is now really simple

sql
SELECT *
FROM EODData
WHERE SymbolId = 1
AND IsEndOfWeek = 1
ORDER BY SomeDate
```

### Charting intraday data

If we want data for a 1 day chart then we are going to grab in 1 minute intervals, if we are going to chart 5 days we will grab in 5 minute chunks.

There is going to be a lot going on in the following code snippet so I will try to explain it in the comments

sql
DECLARE @StartDate DATETIME = '2011-03-01 09:30:00.000'
DECLARE @TimeSpan INT = 5
-- 1  will return 2011-03-31 00:00:00.000
-- 5  will return 2011-03-25 00:00:00.000

--Grab latest 1 or 5 days, we have to account for weekends and markets being closed, this is why we do @TimeSpan * 5
-- and then we do top @TimeSpan..which can be 1 or 5
-- we convert to date so that we get distinct dates
SELECT  TOP (@TimeSpan) @StartDate = Today 
	FROM
		(SELECT  DISTINCT TOP (@TimeSpan * 5) CONVERT(DATE,TickTime) AS Today
		FROM dbo.TickData
		WHERE SymbolID =  1
		AND TickTime > @StartDate 
		ORDER BY CONVERT(DATE,TickTime) DESC) x
	ORDER BY Today DESC
	

--We have t and q for column names because this is being generated as JSON and we want the data to be as small as possible
SELECT 
	t1.TickTime AS t,
	t1.TickPrice AS q
	FROM dbo.TickData t1
	JOIN
		(SELECT SymbolID, MAX(TickTime) AS Ticktime,DATEPART(mi,ticktime) AS TickMinute
		FROM dbo.TickData
		WHERE SymbolID =  1
		AND ticktime >= @StartDate
		GROUP BY SymbolID,DATEPART(hh,TickTime),DATEPART(mi,ticktime),DATEPART(dd,ticktime)) x
	ON x.SymbolID = t1.SymbolID
	AND x.Ticktime = t1.Ticktime
	WHERE t1.SymbolID = 1
	AND TickMinute %  @TimeSpan = 0  --0nly grab the minutes what the value of @TimeSpan holds
	ORDER BY t1.TickTime
```
Take a look at this line AND TickMinute % @TimeSpan = 0
  
So basically we are aggregating in 1 or 5 minutes (really whatever @TimeSpan is, if it is 3 then it will be in 3 minute chunks). We are using the % [(Modulo)][4] operator to accomplish this.

–If you run the code above with @TimeSpan = 5, you get data in 5 minute intervals
  
DECLARE @TimeSpan INT = 5

<div class="tables">
  <table>
    <tr>
      <th>
        t
      </th>
      
      <th>
        q
      </th>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:30:30.000
      </td>
      
      <td>
        17132.3582356171
      </td>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:35:30.000
      </td>
      
      <td>
        17132.3596056885
      </td>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:40:30.000
      </td>
      
      <td>
        17132.3609757598
      </td>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:45:30.000
      </td>
      
      <td>
        17132.3623458311
      </td>
    </tr>
  </table>
</div>

–If you run the code above with @TimeSpan = 1, you get data in 1 minute intervals
  
DECLARE @TimeSpan INT = 1

<div class="tables">
  <table>
    <tr>
      <th>
        t
      </th>
      
      <th>
        q
      </th>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:30:30.000
      </td>
      
      <td>
        17132.3582356171
      </td>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:31:30.000
      </td>
      
      <td>
        17132.3585096314
      </td>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:32:30.000
      </td>
      
      <td>
        17132.3587836457
      </td>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:33:30.000
      </td>
      
      <td>
        17132.3590576599
      </td>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:34:30.000
      </td>
      
      <td>
        17132.3593316742
      </td>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:35:30.000
      </td>
      
      <td>
        17132.3596056885
      </td>
    </tr>
    
    <tr>
      <td>
        2011-03-31 09:36:30.000
      </td>
      
      <td>
        17132.3598797027
      </td>
    </tr>
  </table>
</div>

That is it for this post, there is a lot of code but hopefully you can get an idea of what it all does, if you have any questions leave me a comment.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][5] forum or our [Microsoft SQL Server Admin][6] forum**<ins></ins>

 [1]: /wp-content/uploads/blogs/DataMgmt/olap_1.gif
 [2]: /index.php/DataMgmt/DBProgramming/come-one-come-all-to
 [3]: /index.php/DataMgmt/?disp=authdir&author=420
 [4]: http://msdn.microsoft.com/en-us/library/ms190279.aspx
 [5]: http://forum.ltd.local/viewforum.php?f=17
 [6]: http://forum.ltd.local/viewforum.php?f=22