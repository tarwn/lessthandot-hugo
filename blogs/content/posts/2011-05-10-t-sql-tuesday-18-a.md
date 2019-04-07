---
title: 'T-SQL Tuesday #18: A Recursive CTE Is: A Recursive CTE Is'
author: Jes Borland
type: post
date: 2011-05-10T11:18:00+00:00
ID: 1167
excerpt: "This month's T-SQL Tuesday topic is a favorite of mine: CTEs. I show you how to write a recursive CTE to insert dates into a dates table."
url: /index.php/datamgmt/dbprogramming/t-sql-tuesday-18-a/
views:
  - 15349
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server

---
[![][1]][2]

Welcome to T-SQL Tuesday #18, hosted by Bob Pusateri ([blog][3] | [twitter][4]). Bob, thanks for hosting. From past experience, I know it takes time and effort. 

Bob asked us to write about something I embraced many years ago: Common Table Expressions (CTEs). “Have you ever solved or created a problem by using CTEs? Got a tip, trick, or something nifty to share? I’d love to see your posts about any of the above.” 

I’ve used CTEs for many things, but always avoided learning a recursive CTE. So, I challenged myself to sit down, find a use for one, write the code for it, and blog it. Challenge met. 

<p align="center">
  <img src="/wp-content/uploads/users/grrlgeek/RecursionAgain250x200.png?mtime=1304994446" alt="" title="" />
</p>

I didn’t want to go down the manager/employee or order/item path. Those have been done, many times, and I’m not interested in them. Then I remembered a challenge I’d run into at my previous job that I had always looked for a new solution to: building a dates table. (I am not going to go into whether or not this is the _best_ way or the _most efficient_ way or the _prettiest_ way to build this table. I believe those debates have been raging for years, and I have nothing new to add to any side of the argument. I simply wanted to _do it_.) 

**What I Want** 

_(If a woman can claim to know what she wants.)_ I want a table that shows me the date, and several pieces of information: the year, quarter, month, week, day of the week, day and day of the year. All of these can be found using DATEPART. My base query is:

```sql
DECLARE @GoDate DATE = GETDATE()
SELECT @GoDate, 
	DATEPART(YY, @GoDate), 
	DATEPART(QQ, @GoDate), 
	DATEPART(MM, @GoDate), 
	DATEPART(WW, @GoDate), 
	DATEPART(DW, @GoDate), 
	DATEPART(DD, @GoDate), 
	DATEPART(DY, @GoDate)
```

![][5]

**Step 1: A Recursive CTE** 

First, I’m going to build a recursive CTE to select the current date, and then another year’s worth of dates. 

```sql
DECLARE @GoDate DATE = GETDATE()
;WITH DateCTE (CalendarDate, DateYear, DateQuarter, DateMonth, DateWeek, DateDayOfWeek, DateDay, DateDayOfYear) AS 
( SELECT @GoDate, 
	DATEPART(YY, @GoDate), 
	DATEPART(QQ, @GoDate), 
	DATEPART(MM, @GoDate), 
	DATEPART(WW, @GoDate), 
	DATEPART(DW, @GoDate), 
	DATEPART(DD, @GoDate), 
	DATEPART(DY, @GoDate) 
  UNION ALL 
  SELECT DATEADD(DD, 1, CalendarDate), 
	DATEPART(YY, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(QQ, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(MM, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(WW, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DW, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DD, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DY, DATEADD(DD, 1, CalendarDate)) 
  FROM DateCTE 
)
SELECT CalendarDate, DateYear, DateQuarter, DateMonth, DateWeek, DateDayOfWeek, DateDay, DateDayOfYear
FROM DateCTE AS DC 
OPTION(MAXRECURSION 365);
GO
```

**Breaking It Down** 

I declare my starting date variable (@GoDate), and define my CTE, listing what columns I want to return.

```sql
DECLARE @GoDate DATE = GETDATE()
;WITH DateCTE (CalendarDate, DateYear, DateQuarter, DateMonth, DateWeek, DateDayOfWeek, DateDay, DateDayOfYear) AS
```

The first query is the base query, to define where the recursion will start – the **anchor member**. 

```sql
( SELECT @GoDate, 
	DATEPART(YY, @GoDate), 
	DATEPART(QQ, @GoDate), 
	DATEPART(MM, @GoDate), 
	DATEPART(WW, @GoDate), 
	DATEPART(DW, @GoDate), 
	DATEPART(DD, @GoDate), 
	DATEPART(DY, @GoDate) 
```

Then, the beauty of a CTE: it can reference itself, as I demonstrate with the UNION ALL and second query. This is the **recursive member**. Note that my FROM is not another table, but rather DateCTE. 

```sql
UNION ALL 
  SELECT DATEADD(DD, 1, CalendarDate), 
	DATEPART(YY, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(QQ, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(MM, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(WW, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DW, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DD, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DY, DATEADD(DD, 1, CalendarDate)) 
  FROM DateCTE 
)
```

The final piece is a query, which is the result of all sets returned by the UNION ALL. In this query, I could also join to other tables, another really beautiful part of the CTE. (I find this especially useful when using CTEs for aggregation. Not to distract you. Or me. I have to finish this post first.) 

Because I don’t have a WHERE clause in my second query, this could be an infinite loop. (Unless you believe the world is going to end on December 21, 2012. But that wasn’t coded into SQL Server.) How do I prevent this? I use the query hint OPTION (MAXRECURSION X). 

What I learned while writing this post: if not explicitly specified, the default MAXRECURSION is 100. The range is 0 – 32,767. 0 indicates “no limit”. Also, and I quote from [Books Online][6], “When the specified or default number for MAXRECURSION limit is reached during query execution, the query is ended and an error is returned. Because of this error, all effects of the statement are rolled back. If the statement is a SELECT statement, partial results or no results may be returned. Any partial results returned may not include all rows on recursion levels beyond the specified maximum recursion level.” This will come back to haunt me later, as you will see. 

```sql
SELECT CalendarDate, DateYear, DateQuarter, DateMonth, DateWeek, DateDayOfWeek, DateDay, DateDayOfYear
FROM DateCTE AS DC 
OPTION(MAXRECURSION 365);
GO
```

Here are my query results. Note that the count is 366 rows: the original anchor row, plus 365 recursions. ![][7]

**Step 2: The Dates Table** 

The query results aren’t very useful if you have to run the query every time you want to use it. Solution: build a table! 

```sql
CREATE TABLE Dates
(CalendarDate DATE NOT NULL, 
 DateYear INT NOT NULL, 
 DateQuarter INT NOT NULL, 
 DateMonth INT NOT NULL, 
 DateWeek INT NOT NULL, 
 DateDayOfWeek INT NOT NULL, 
 DateDay INT NOT NULL, 
 DateDayOfYear INT NOT NULL)
```

Next, I run the CTE query again, but this time with an INSERT instead of a SELECT. 

```sql
DECLARE @GoDate DATE = GETDATE()
;WITH DateCTE (CalendarDate, DateYear, DateQuarter, DateMonth, DateWeek, DateDayOfWeek, DateDay, DateDayOfYear) AS 
( SELECT @GoDate, 
	DATEPART(YY, @GoDate), 
	DATEPART(QQ, @GoDate), 
	DATEPART(MM, @GoDate), 
	DATEPART(WW, @GoDate), 
	DATEPART(DW, @GoDate), 
	DATEPART(DD, @GoDate), 
	DATEPART(DY, @GoDate) 
  UNION ALL 
  SELECT DATEADD(DD, 1, CalendarDate), 
	DATEPART(YY, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(QQ, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(MM, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(WW, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DW, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DD, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DY, DATEADD(DD, 1, CalendarDate)) 
  FROM DateCTE 
)
INSERT INTO Dates 
SELECT CalendarDate, DateYear, DateQuarter, DateMonth, DateWeek, DateDayOfWeek, DateDay, DateDayOfYear
FROM DateCTE AS DC  
OPTION(MAXRECURSION 365);
GO 
```

Uh-oh! It blew up! 

```sql
Msg 530, Level 16, State 1, Line 2
The statement terminated. The maximum recursion 365 has been exhausted before statement completion.
```

The MAXRECURSION level was reached, so the results are rolled back. (Remember when I said this would come back to haunt me? This is the ghost.) 

My solution is to set a WHERE clause in the recursive query, limiting the number of days the query is run for. 

```sql
DECLARE @GoDate DATE = GETDATE()
;WITH DateCTE (CalendarDate, DateYear, DateQuarter, DateMonth, DateWeek, DateDayOfWeek, DateDay, DateDayOfYear) AS 
( SELECT @GoDate, 
	DATEPART(YY, @GoDate), 
	DATEPART(QQ, @GoDate), 
	DATEPART(MM, @GoDate), 
	DATEPART(WW, @GoDate), 
	DATEPART(DW, @GoDate), 
	DATEPART(DD, @GoDate), 
	DATEPART(DY, @GoDate) 
  UNION ALL 
  SELECT DATEADD(DD, 1, CalendarDate), 
	DATEPART(YY, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(QQ, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(MM, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(WW, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DW, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DD, DATEADD(DD, 1, CalendarDate)), 
	DATEPART(DY, DATEADD(DD, 1, CalendarDate)) 
  FROM DateCTE 
  WHERE DATEADD(DD, 1, CalendarDate) <= DATEADD(DD, 365, @GoDate)
)
INSERT INTO Dates 
SELECT CalendarDate, DateYear, DateQuarter, DateMonth, DateWeek, DateDayOfWeek, DateDay, DateDayOfYear
FROM DateCTE AS DC 
OPTION(MAXRECURSION 365);
GO
```

I check my results in the table: 

```sql
SELECT CalendarDate, DateYear, DateQuarter, DateMonth, DateWeek, DateDayOfWeek, DateDay, DateDayOfYear
FROM Dates 
ORDER BY CalendarDate
```

My results:

![][8]

I have successfully inserted 366 rows into my Dates table. 

**Ta-Da!** 

I’m glad I took the time to break down the recursive CTE and learn how to use it. I know I’ll use it in the future. The CTE has limitations, and is not always the most efficient SQL, but it is useful and flexible. I suggest you learn to write CTEs and recursive CTEs, to have another tool in your SQL toolbox.

 [1]: /wp-content/uploads/blogs/DataMgmt/olap_1.gif ""
 [2]: http://www.bobpusateri.com/archive/2011/04/invitation-to-t-sql-tuesday-18-ctes/
 [3]: http://www.bobpusateri.com
 [4]: http://twitter.com/sqlbob
 [5]: /wp-content/uploads/users/grrlgeek/DateCTE1.JPG?mtime=1304994784 ""
 [6]: http://msdn.microsoft.com/en-us/library/ms181714.aspx
 [7]: /wp-content/uploads/users/grrlgeek/DateCTE2.JPG?mtime=1304994887 ""
 [8]: /wp-content/uploads/users/grrlgeek/DateCTE4.JPG?mtime=1304994888 ""