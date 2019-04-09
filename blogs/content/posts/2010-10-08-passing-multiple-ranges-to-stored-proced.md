---
title: Passing multiple ranges to stored procedure
author: Naomi Nosonovsky
type: post
date: 2010-10-08T12:25:02+00:00
ID: 917
excerpt: |
  This blog was inspired by the following thread at MSDN Transact-SQL forum:
  Multiple values in field as parameter
  
  Given a string with values such as '201|203|301|400..600|725|800..900' return records from the table where Code field will be any of the&hellip;
url: /index.php/datamgmt/datadesign/passing-multiple-ranges-to-stored-proced/
views:
  - 9325
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
This blog was inspired by the following thread at MSDN Transact-SQL forum:
  
[Multiple values in field as parameter][1]

Given a string with values such as &#8216;201|203|301|400..600|725|800..900' return records from the table where Code field will be any of the values passed with | as a delimiter and also within the passed ranges where range indication will be ..

The first idea that comes to mind is to use various splitting techniques available (see, for example, this excellent blog by Aaron Bertrand [Splitting List of Integers][2] or follow up by Brad Schulz [Integer List Split &#8211; a SQL fable][3] or the old times classic by Erland Sommarskog [Arrays and Lists in SQL Server][4]) split the list first by | and then, if needed by .. to introduce the ranges of values.

In order to run the solution I implemented, we need to create a Numbers table in our database first (why it's important you can read in this classical article
  
[Why should I consider using an auxiliary Calendar table][5]).

Here is a script I use to create both Numbers and Calendar tables once in the Model database so all other user databases will inherit them:

sql
USE model
GO

CREATE TABLE dbo.numbers (
  number int NOT NULL
)

ALTER TABLE dbo.numbers
ADD
  CONSTRAINT pk_numbers PRIMARY KEY CLUSTERED (number)
   WITH FILLFACTOR = 100
GO

INSERT INTO dbo.numbers (number)
SELECT (a.number * 256) + b.number As number
FROM 	 (
    SELECT number
    FROM  master..spt_values
    WHERE type = 'P'
    AND  number <= 255
    ) As a
 CROSS
 JOIN (
    SELECT number
    FROM  master..spt_values
    WHERE type = 'P'
    AND  number <= 255
    ) As b
GO

CREATE TABLE dbo.calendar (
  the_date   datetime NOT NULL
 , is_monday  bit   NOT NULL
 , is_tuesday  bit   NOT NULL
 , is_wednesday bit   NOT NULL
 , is_thursday bit   NOT NULL
 , is_friday  bit   NOT NULL
 , is_saturday bit   NOT NULL
 , is_sunday  bit   NOT NULL
 , is_weekend As (is_saturday ^ is_sunday)
 , is_holiday  bit
 , holiday_desc varchar(50)
)
GO

ALTER TABLE dbo.calendar
ADD
  CONSTRAINT pk_calendar PRIMARY KEY CLUSTERED (the_date)
   WITH FILLFACTOR = 100
GO

INSERT INTO dbo.calendar (the_date, is_monday, is_tuesday, is_wednesday, is_thursday, is_friday, is_saturday, is_sunday)
SELECT the_date
   , CASE WHEN DateDiff(dd, 0, the_date) % 7 = 0 THEN 1 ELSE 0 END As is_monday
   , CASE WHEN DateDiff(dd, 0, the_date) % 7 = 1 THEN 1 ELSE 0 END As is_tuesday
   , CASE WHEN DateDiff(dd, 0, the_date) % 7 = 2 THEN 1 ELSE 0 END As is_wednesday
   , CASE WHEN DateDiff(dd, 0, the_date) % 7 = 3 THEN 1 ELSE 0 END As is_thursday
   , CASE WHEN DateDiff(dd, 0, the_date) % 7 = 4 THEN 1 ELSE 0 END As is_friday
   , CASE WHEN DateDiff(dd, 0, the_date) % 7 = 5 THEN 1 ELSE 0 END As is_saturday
   , CASE WHEN DateDiff(dd, 0, the_date) % 7 = 6 THEN 1 ELSE 0 END As is_sunday
FROM  (
    SELECT DateAdd(dd, number, 0) As the_date
    FROM  dbo.numbers
    ) As dates
```

Now, we need to create a function that will split the values. I chose the Numbers table variation of this function:

sql
CREATE FUNCTION [dbo].[fnSplit2](
  @List       VARCHAR(MAX),
   @Delimiter  VARCHAR(10)
)
RETURNS TABLE
AS
   RETURN
   (
       SELECT ROW_NUMBER() over (order by Number) as Pos,
           Value = LTRIM(RTRIM(
               SUBSTRING(@List, Number,
               CHARINDEX(@Delimiter, @List + @Delimiter, Number) - Number)))
       FROM
           dbo.Numbers WITH (NOLOCK)
       WHERE
           Number <= CONVERT(INT, LEN(@List))
           AND SUBSTRING(@Delimiter + @List, Number, LEN(@Delimiter)) = @Delimiter
   );
GO
```

And now we can create our final solution:

sql
use AdventureWorks
--select * from Sales.SalesOrderHeader order by CustomerID

declare @Param varchar(max) 
set @Param = '1|2|5|10..20|43|70..86|102'

declare @t table (iPos int, cValue varchar(max)) 

   insert into @t select * from dbo.fnSplit2(@Param, '|')

   if not exists (select 1 from @t where cValue like '%..%') -- we don't have to split by ..

      select distinct S.CustomerID from Sales.SalesOrderHeader S inner join @t T on S.CustomerID = T.cValue
      ORDER BY S.CustomerID 

   else

        begin

                -- we need to split by .. now
              if OBJECT_ID('TempDB..#TempRanges','U') IS NOT NULL
                  drop table #TempRanges  
               
   --            select * from @t    
               select iPos, cValue, max(case when Pos = 1 then Value end) as MinVal,

              max(case when Pos = 2 then Value end) as MaxVal into #TempRanges

            from (

select   T.*, F.Pos, F.Value from @t T OUTER apply dbo.fnSplit2(T.cValue, '..') F ) X

 GROUP BY iPos, cValue
 
 --select * from #TempRanges 

            --select T1.CustomerID from Sales.SalesOrderHeader T1 where exists

            --(select 1 from #TempRanges X where cValue not like '%..%'   and T1.CustomerID = X.cValue)

            --union

            --select T.CustomerID from Sales.SalesOrderHeader T 
            --inner join #TempRanges X on 
            --T.CustomerID between X.MinVal and X.MaxVal and X.cValue LIKE '%..%'
            --ORDER BY CustomerID 
            
            select distinct T.CustomerID from Sales.SalesOrderHeader T 
            where  exists (select 1 from #TempRanges X where 
            T.CustomerID between X.MinVal and coalesce(X.MaxVal, X.MinVal)) 
            ORDER BY CustomerID 
            
     end
```

There is a simpler XML based solution presented by Peso (Peter Larsson) in the following  [MSDN Thread][6]:

sql
DECLARE	@Sample TABLE
	(
		ID INT,
		Data VARCHAR(100)
	)

INSERT	@Sample
VALUES	(1, '1000|5000')

DECLARE	@ID INT = 1

-- Solution here
;WITH cteSource(ID, Data)
AS (
	SELECT	ID,
		CAST('<v><i>' + REPLACE(REPLACE(Data, '|', '</i></v><v><i>'), '..', '</i><i>') + '</i></v>' AS XML) AS Data
	FROM	@Sample
	WHERE	ID = @ID
)
SELECT		s.ID,
		v.value('i[1]', 'INT') AS [Start],
		COALESCE(v.value('i[2]', 'INT'), v.value('i[1]', 'INT')) AS [End]
FROM		cteSource AS s
CROSS APPLY	Data.nodes('v') AS n(v)
```
\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][7] forum or our [Microsoft SQL Server Admin][8] forum**<ins></ins>

 [1]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/0413b1c2-b7e9-45a2-8d3c-f09adc3d672a
 [2]: http://sqlblog.com/blogs/aaron_bertrand/archive/2010/07/07/splitting-a-list-of-integers-another-roundup.aspx
 [3]: http://bradsruminations.blogspot.com/2010/08/integer-list-splitting-sql-fable.html
 [4]: http://www.sommarskog.se/arrays-in-sql.html
 [5]: http://sqlserver2000.databases.aspfaq.com/why-should-i-consider-using-an-auxiliary-calendar-table.html
 [6]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/165a4dc0-8d50-45c8-87b7-f34db50e6197
 [7]: http://forum.ltd.local/viewforum.php?f=17
 [8]: http://forum.ltd.local/viewforum.php?f=22