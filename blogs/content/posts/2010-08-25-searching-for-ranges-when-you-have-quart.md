---
title: Searching for ranges when you have quarters and years
author: SQLDenis
type: post
date: 2010-08-25T07:36:04+00:00
ID: 843
url: /index.php/datamgmt/dbprogramming/searching-for-ranges-when-you-have-quart/
views:
  - 6692
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - calendar
  - dates
  - how to
  - quarters

---
Someone posted a question, they wanted to return the quarters and years within a range that were passed in. The problem they had is that they stored this data in a year and a quarter column. The table looked like this

sql
CREATE TABLE Periods(PeriodQuarter INT,PeriodYear INT)
INSERT Periods VALUES (1,2009)
INSERT Periods VALUES (2,2009)
INSERT Periods VALUES (3,2009)
INSERT Periods VALUES (4,2009)
INSERT Periods VALUES (1,2010)
INSERT Periods VALUES (2,2010)
INSERT Periods VALUES (3,2010)
GO
CREATE CLUSTERED INDEX ix_Periods on Periods(PeriodYear,PeriodQuarter)
GO
```

When we do this simple select query

sql
SELECT *
FROM Periods
```

We get the following table.

<div class="tables">
  <table>
    <tr>
      <th>
        PeriodQuarter
      </th>
      
      <th>
        PeriodYear
      </th>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2009
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2009
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2009
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        2009
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2010
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2010
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2010
      </td>
    </tr>
  </table>
</div>

If we pass in a range from _2009-01-01_ until _2009-09-28_, then the following data should be returned:

<div class="tables">
  <table>
    <tr>
      <th>
        PeriodQuarter
      </th>
      
      <th>
        PeriodYear
      </th>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2009
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2009
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2009
      </td>
    </tr>
  </table>
</div>

## Running the queries

There are a couple of ways to return that data – below are 3 queries and their execution plans

The first query creates a date from the 2 columns and then checks if that date falls between the end and start date passed in.
  
Run the statement below, it returns '2009-01-01 00:00:00.000'. Mess around with the numbers to to see how it works.

sql
SELECT DATEADD(qq,1-1,DATEADD(yy,2009 -1900,0))
```

**Query 1** 
  
Here is the first query:

sql
declare @startDate datetime
declare @endDate datetime

select @startDate = '20090101',@endDate = '20090928'

SELECT *
FROM Periods
WHERE  DATEADD(qq,PeriodQuarter-1,DATEADD(yy,PeriodYear -1900,0)) 
BETWEEN @startDate AND @endDate
GO
```

This is the execution plan, as you can see it uses a Clustered Index Scan

> |–Clustered Index Scan(OBJECT:([msdb].[dbo].[Periods].[ix_Periods]),
    
> WHERE:(dateadd(quarter,[msdb].[dbo].[Periods].[PeriodQuarter]-(1),
    
> dateadd(year,[msdb].[dbo].[Periods].[PeriodYear]-(1900),'1900-01-01 00:00:00.000'))>=[@startDate]
    
> AND dateadd(quarter,[msdb].[dbo].[Periods].[PeriodQuarter]-(1),
    
> dateadd(year,[msdb].[dbo].[Periods].[PeriodYear]-(1900),'1900-01-01 00:00:00.000'))<=[@endDate])) 

**Query 2** 
  
Query 2 is a little smarter, it checks for the year which is the first key in the composite clustered index and thus avoids a Clustered Index Scan like the query above.

sql
declare @startDate datetime
declare @endDate datetime

select @startDate = '20090101',@endDate = '20090928'

SELECT *
FROM Periods
WHERE  PeriodYear between YEAR(@startDate) and YEAR(@endDate)
AND DATEADD(qq,PeriodQuarter-1,DATEADD(yy,PeriodYear -1900,0)) 
BETWEEN @startDate AND @endDate
GO
```

Here is the plan, as you can see it results in a Clustered Index Seek.

> |–Clustered Index Seek(OBJECT:([msdb].[dbo].[Periods].[ix_Periods]),
    
> SEEK:([msdb].[dbo].[Periods].[PeriodYear] >= datepart(year,[@startDate])
    
> AND [msdb].[dbo].[Periods].[PeriodYear] <= datepart(year,[@endDate])), WHERE:(dateadd(quarter,[msdb].[dbo].[Periods].[PeriodQuarter]-(1), dateadd(year,[msdb].[dbo].[Periods].[PeriodYear]-(1900),'1900-01-01 00:00:00.000'))>=[@startDate]
    
> AND dateadd(quarter,[msdb].[dbo].[Periods].[PeriodQuarter]-(1),
    
> dateadd(year,[msdb].[dbo].[Periods].[PeriodYear]-(1900),'1900-01-01 00:00:00.000'))<=[@endDate]) ORDERED FORWARD)

**Query 3** 
  
Query 3 is very similar to query 2 but instead of dateadd it uses arithmetic to grab the correct rows

sql
SELECT *
FROM Periods
WHERE
       PeriodYear BETWEEN YEAR(@startdate) AND YEAR(@enddate)
       AND PeriodYear * 4 + PeriodQuarter
               BETWEEN Year(@startdate) * 4 + DATEPART(Quarter, @startdate)
               AND Year(@enddate) * 4 + DATEPART(Quarter, @enddate)
```
Here is the plan, as you can see it results in a Clustered Index Seek also.

> |–Clustered Index Seek(OBJECT:([msdb].[dbo].[Periods].[ix_Periods]),
      
> SEEK:([msdb].[dbo].[Periods].[PeriodYear] >= datepart(year,[@startDate])
      
> AND [msdb].[dbo].[Periods].[PeriodYear] <= datepart(year,[@endDate])), WHERE:(([msdb].[dbo].[Periods].[PeriodYear]\*(4)+[msdb].[dbo].[Periods].[PeriodQuarter]) >=datepart(year,[@startDate])\*(4)+datepart(quarter,[@startDate])
      
> AND ([msdb].[dbo].[Periods].[PeriodYear]*(4)+[msdb].[dbo].[Periods].[PeriodQuarter])
      
> <=datepart(year,[@enddate])*(4)+datepart(quarter,[@endDate])) ORDERED FORWARD) 

## Another approach

Instead of converting and doing arithmetic, you could add a computed column to the table

sql
ALTER TABLE Periods ADD PeriodDate AS DATEADD(qq,PeriodQuarter-1,DATEADD(yy,PeriodYear -1900,0))  
GO
```

Now, when we query the table

sql
SELECT *
FROM Periods
```

… the data looks like this

<div class="tables">
  <table>
    <tr>
      <th>
        PeriodQuarter
      </th>
      
      <th>
        PeriodYear
      </th>
      
      <th>
        PeriodDate
      </th>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2009
      </td>
      
      <td>
        2009-01-01 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2009
      </td>
      
      <td>
        2009-01-01 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2009
      </td>
      
      <td>
        2009-01-01 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        4
      </td>
      
      <td>
        2009
      </td>
      
      <td>
        2009-01-01 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        1
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        2010-01-01 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        2
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        2010-01-01 00:00:00.000
      </td>
    </tr>
    
    <tr>
      <td>
        3
      </td>
      
      <td>
        2010
      </td>
      
      <td>
        2010-01-01 00:00:00.000
      </td>
    </tr>
  </table>
</div>

Now the query is much simpler

sql
DECLARE @startDate DATETIME
DECLARE @endDate DATETIME

SELECT @startDate = '20090101',@endDate = '20090928'
SELECT *
FROM Periods
WHERE PeriodDate BETWEEN @startDate AND @endDate
```

> |–Clustered Index Scan(OBJECT:([msdb].[dbo].[Periods].[ix_Periods]), WHERE:(dateadd(quarter,[msdb].[dbo].[Periods].[PeriodQuarter]-(1),dateadd(year,[msdb].[dbo].[Periods].[PeriodYear]-(1900),'1900-01-01 00:00:00.000'))>=[@startDate] AND dateadd(quarter,[msdb].[dbo].[Periods].[PeriodQuarter]-(1),dateadd(year,[msdb].[dbo].[Periods].[PeriodYear]-(1900),'1900-01-01 00:00:00.000'))<=[@endDate])) 

So we still have an index scan, but if we create an index on the computed column now, we can find out if that helps.

sql
CREATE INDEX ix_PeriodDate ON Periods(PeriodDate)
GO
```

Now, if we run the same query again.

sql
DECLARE @startDate DATETIME
DECLARE @endDate DATETIME

SELECT @startDate = '20090101',@endDate = '20090928'

SELECT *
FROM Periods
WHERE PeriodDate BETWEEN @startDate AND @endDate
```
> |–Index Seek(OBJECT:([msdb].[dbo].[Periods].[ix_PeriodDate]), SEEK:([msdb].[dbo].[Periods].[PeriodDate] >= [@startDate] AND [msdb].[dbo].[Periods].[PeriodDate] <= [@endDate]) ORDERED FORWARD)

And there is your index seek.

Just as an FYI, don't just start creating computed columns and adding indexes left and right. If you can, try to modify the table so that you store the dates only; after that it is easy with the _datepart_ function to figure out what year, quarter or month is stored in that column.

To keep this post to a reasonable length, I decided to create another post that will show you how to use a calendar table to the same kind of query. That post will be up in a day or two.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22