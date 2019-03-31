---
title: Script to Populate Date Dimension, without Using a Cursor
author: Sam Vanga
type: post
date: 2013-01-22T13:20:00+00:00
excerpt: "Most of the scripts I've used to populate date dimension uses a cursor. Since data is loaded only once to a date dimension in the ETL life cycle, using a cursor isn't a sin. However, if you want to kick cursors out of the park, here is an alternative using Window Functions."
url: /index.php/datamgmt/dbprogramming/script-to-populate-date-dimension-sql-server/
views:
  - 9762
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
tags:
  - data warehouse

---
Most of the scripts I&#8217;ve used to populate date dimension uses a cursor. Since data is loaded only once to a date dimension in the ETL life cycle, using a cursor isn&#8217;t a sin.

Still, when I was reviewing my own code the other day, I wanted to get rid of the cursor. _Why not, Right_?

Here is a script that uses CTE and Window Functions to populate the date dimension.

<pre>DECLARE @startdate DATE = '20000101'
	, @enddate DATE = '20301231' ;

WITH c
AS (
	SELECT	Num = ROW_NUMBER() OVER (ORDER BY (SELECT NULL)) - 1
	FROM sys.columns c
	CROSS JOIN sys.columns c1
	)
, d
AS (
	SELECT	[date] = DATEADD(day, Num, @startdate)
			, Num
	FROM c
	WHERE Num &gt;= 0
		AND Num &lt;= DATEDIFF(day, @startdate, @enddate)
	)
SELECT datekey = CAST(CONVERT(VARCHAR(8), DATEADD(day, Num, @startdate), 112) AS INT)
	, [date]
	, [DayOfMonth] = DATEPART(day, [Date])
	, [DayName] = DATENAME(weekday, [Date])
	, [DayOfYear] = DATEPART(dayofyear, [Date])
	, [WeekOfYear] = DATEPART(week, [Date])
	, [MonthName] = DATENAME(month, [Date])
	, [MonthNumber] = DATEPART(month, [Date])
	, [QuarterNumber] = DATEPART(quarter, [Date])
	, [Year] = YEAR([date])
	, [FiscalYear] = CASE 
		WHEN DATEPART(month, [Date]) &lt; 7
			THEN YEAR([date])
		ELSE YEAR([date]) + 1
		END
--Add more columns as needed.
FROM d</pre>

Let&#8217;s take a closer look:

  1. Variables StartDate and EndDate hold the date range for the result set.
  2. We define a CTE (called c) that uses Row_Number() function to generate numbers starting from zero for each row in sys.columns. sys.columns is cross joined to itself to generate more number of [input] rows.
  3. We define another CTE (called d) that uses numbers from c, filters on date range, and converts numbers to dates based on the date variables.
  4. Finally, the Select statement uses Num and Date column aliases created by the two CTEs and date functions to create additional fields for the date dimension.

You&#8217;ll notice that the script doesn&#8217;t have all columns a date dimension typically has. Don&#8217;t call me lazy for that &#8211; it provides the logic and skeleton to add more columns. Just add them as needed.