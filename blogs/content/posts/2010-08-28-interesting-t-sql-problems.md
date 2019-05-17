---
title: Interesting T-SQL problems
author: Naomi Nosonovsky
type: post
date: 2010-08-29T03:28:14+00:00
ID: 890
excerpt: |
  With this blog post I am hoping to start a new series of blogs devoted to the interesting T-SQL problems I encounter in forums during the week.
  
  The idea of this blog series came to me on Wednesday night when I thought I solved a complex problem...&hellip;
url: /index.php/datamgmt/datadesign/interesting-t-sql-problems/
views:
  - 18104
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
With this blog post I am hoping to start a new series of blogs devoted to the interesting T-SQL problems I encounter in forums during the week.

The idea of this blog series came to me on Wednesday night when I thought I solved a complex problem...

# First Problem – Divide data into 15 min. time intervals

The first problem, I'd like to discuss, is found in this [MSDN thread][1]:

Given this table

<div class="tables">
  <table border="1" cellspacing="2" cellpadding="2">
    <tr>
      <th>
        SalesDateTime
      </th>
      
      <th>
        SalesAmount
      </th>
    </tr>
    
    <tr>
      <td>
        2010-08-10 00:05:12
      </td>
      
      <td>
        58.22
      </td>
    </tr>
    
    <tr>
      <td>
        2010-08-10 00:08:22
      </td>
      
      <td>
        21.10
      </td>
    </tr>
    
    <tr>
      <td>
        2010-08-10 00:09:38
      </td>
      
      <td>
        8.45
      </td>
    </tr>
    
    <tr>
      <td>
        2010-08-10 00:18:04
      </td>
      
      <td>
        9.52
      </td>
    </tr>
    
    <tr>
      <td>
        2010-08-10 00:19:56
      </td>
      
      <td>
        45.20
      </td>
    </tr>
    
    <tr>
      <td>
        2010-08-10 11:35:15
      </td>
      
      <td>
        47.12
      </td>
    </tr>
    
    <tr>
      <td>
        2010-08-10 11:36:12
      </td>
      
      <td>
        88.55
      </td>
    </tr>
    
    <tr>
      <td>
        2010-08-10 11:40:31
      </td>
      
      <td>
        45.12
      </td>
    </tr>
  </table>
</div>

find the Average Sale amount for the 15 minutes time interval.

The first idea, that comes to mind, of how to solve this problem, is to use integer math in T-SQL. In T-SQL, unlike other languages, when you divide one integer by another integer, you get an integer in return.

So, if we divide datepart(minute,SalesDateTime) by 15, we will organize the data into the 15 minutes intervals.

Now, my first idea was to use datepart function to get year, month, day, hour portions of the date and then construct the date based on these parts using concatenation and string functions. Later, upon thinking, I realized we can use CONVERT() function to grab first portion of the datetime field (up to hours), then add Group * 15 to get the minute part and then convert back to datetime. While I was writing this solution and testing it, Tom Cooper came up with the simpler idea based on the datediff function and fixed date.

I list both of the solutions below:

```sql
declare @Sales table (SalesDateTime datetime,     SalesAmount decimal(10,2))
insert into @Sales
select

'2010-08-10 00:05:12',   58.22
union all select
'2010-08-10 00:08:22',   21.10
union all select
'2010-08-10 00:09:38',   8.45

 union all select

'2010-08-10 00:18:04',   9.52
union all select
'2010-08-10 00:19:56',   45.20

 union all select

'2010-08-10 11:35:15',   47.12
union all select
'2010-08-10 11:36:12',   88.55
union all select
'2010-08-10 11:40:31',   45.12
union all select
'2010-08-10 11:52:31', 23.45

-- Naomi's query

;with cte as (select MIN(SalesDateTime) as MinDate,
MAX(SalesDateTime) as MaxDate,
convert(varchar(14),SalesDateTime, 120) as StartDate, DATEPART(minute, SalesDateTime) /15 as GroupID,
avg(SalesAmount) as AvgAmount
from @Sales
group by convert(varchar(14),SalesDateTime, 120),
DATEPART(minute, SalesDateTime) /15)

select dateadd(minute, 15*GroupID, CONVERT(datetime,StartDate+'00')) as [Start Date], 
dateadd(minute, 15*(GroupID+1), CONVERT(datetime,StartDate+'00')) as [End Date],
cast(AvgAmount as decimal(12,2)) as [Average Amount],
MinDate as [Min Date], MaxDate as [Max Date]
from cte

-- Tom Cooper's query
;With cte As
(Select DateAdd(minute, 15 * (DateDiff(minute, '20000101', SalesDateTime) / 15), '20000101') As SalesDateTime, SalesAmount 
From @Sales)
Select SalesDateTime, Cast(Avg(SalesAmount) As decimal(12,2)) As AvegSalesAmount
From cte
Group By SalesDateTime;
```

See also a different and simpler approach suggested by Celko in [this thread][2] of using Time based Calendar table.

* * *

# Second Problem – Find overlapping ranges

The second problem is really a gem and it is presented in [How can I find overlapped ranges?][3] thread:

<div class="tables">
  <table border="1" cellspacing="2" cellpadding="2">
    <tr>
      <th>
        Name
      </th>
      
      <th>
        Chromosome
      </th>
      
      <th>
        Start
      </th>
      
      <th>
        End
      </th>
    </tr>
    
    <tr>
      <td>
        N1
      </td>
      
      <td>
        chr3
      </td>
      
      <td>
        17443613
      </td>
      
      <td>
        17443685
      </td>
    </tr>
    
    <tr>
      <td>
        N2
      </td>
      
      <td>
        chr3
      </td>
      
      <td>
        17443521
      </td>
      
      <td>
        17443685
      </td>
    </tr>
    
    <tr>
      <td>
        N3
      </td>
      
      <td>
        chr2
      </td>
      
      <td>
        162180459
      </td>
      
      <td>
        162180499
      </td>
    </tr>
    
    <tr>
      <td>
        N343
      </td>
      
      <td>
        chr2
      </td>
      
      <td>
        131865573
      </td>
      
      <td>
        131865687
      </td>
    </tr>
    
    <tr>
      <td>
        N34
      </td>
      
      <td>
        chr16
      </td>
      
      <td>
        34623393
      </td>
      
      <td>
        34623610
      </td>
    </tr>
    
    <tr>
      <td>
        N2456
      </td>
      
      <td>
        chr3
      </td>
      
      <td>
        17443512
      </td>
      
      <td>
        17443685
      </td>
    </tr>
    
    <tr>
      <td>
        N43243
      </td>
      
      <td>
        chr3
      </td>
      
      <td>
        17443608
      </td>
      
      <td>
        17443685
      </td>
    </tr>
  </table>
</div>

The whole table is about 31,000 records (includes 22 chromosomes + X, Y chromosomes).

The problem is to find the overlapped regions (or ranges) and these overlapped records have to be on the same Chromosome of this table.

————-
  
I saw this problem at ~11pm on Wednesday night, thought I solved it and that's when the idea of this blog came to my mind.

The next day, however, based on Hunchback's (Alejandro Mesa) comment I realized, that my "solution" worked only for a single overlapping range for the same chromosome. If we have multiple overlapping ranges, that solution will not work. I spent ~ an hour trying to fix my idea for multiple overlapping ranges, but gave up at the end, as I had work to do. So, I will show two ingenious solutions of this problem presented in that thread by (Peso) Peter Larsson.

First let's create the test table with 100K records:

```sql
CREATE TABLE [dbo].[Chromosomes](
    [Name] [varchar](10) NOT NULL,
    [Chromosome] [varchar](10) NOT NULL,
    [iStart] [int] NOT NULL,
    [iEnd] [int] NOT NULL
)
Go
CREATE NONCLUSTERED INDEX [IX_Chromosomes] ON [dbo].[Chromosomes]
(
    [Chromosome] ASC,
    [iStart] ASC,
    [iEnd] ASC
)
INCLUDE ( [Name])
insert into Chromosomes 
SELECT    'N' + CAST(ABS(CHECKSUM(NEWID())) % 5000 AS VARCHAR(12)) AS Name,
    'chr' + CAST(ABS(CHECKSUM(NEWID())) % 22 AS VARCHAR(12)) AS Chromosome,
    iStart,
    iStart + ABS(CHECKSUM(NEWID())) % 10000 AS iEnd
FROM    (
        SELECT    TOP(100000)
            ABS(CHECKSUM(NEWID())) % 200000000 AS iStart
        FROM    Tally
    ) AS d
    
select * from Chromosomes
```

The first solution uses cursor and takes ~27 sec. to execute:

```sql
-- Cursor based idea
set nocount on
declare @TimeStart datetime = getdate()
CREATE TABLE    #Work
        (
            Chromosome VARCHAR(10) NOT NULL,
            FromNum INT NOT NULL,
            ToNum INT NOT NULL,
            Names VARCHAR(MAX) NOT NULL,
            Items INT NOT NULL
        )
 
CREATE CLUSTERED INDEX IX_Chromosome ON #Work (Chromosome, FromNum)
 
DECLARE    curWork CURSOR READ_ONLY FOR    SELECT        Chromosome,
                            iStart,
                            iEnd,
                            Name
                    FROM        dbo.Chromosomes
                    ORDER BY    Chromosome,
                            iStart
 
DECLARE    @FromNum INT,
    @ToNum INT,
    @Chromosome VARCHAR(10),
    @Name VARCHAR(10)
 
OPEN    curWork
 
FETCH    NEXT
FROM    curWork
INTO    @Chromosome,
    @FromNum,
    @ToNum,
    @Name
 
WHILE @@FETCH_STATUS = 0
    BEGIN
        UPDATE    #Work
        SET    FromNum = CASE WHEN FromNum < @FromNum THEN FromNum ELSE @FromNum END,
            ToNum = CASE WHEN ToNum < @ToNum THEN @ToNum ELSE ToNum END,
            Names = CASE WHEN Names LIKE '%, ' + @Name + ', %' THEN Names ELSE Names + ', ' + @Name END,
            Items = Items + 1
        WHERE    Chromosome = @Chromosome
            AND FromNum <= @ToNum
            AND ToNum >= @FromNum
 
        IF @@ROWCOUNT = 0
            INSERT    #Work
                (
                    Chromosome,
                    FromNum,
                    ToNum,
                    Names,
                    Items
                )
            VALUES    (
                    @Chromosome,
                    @FromNum,
                    @ToNum,
                    @Name,
                    1
                )
 
        FETCH    NEXT
        FROM    curWork
        INTO    @Chromosome,
            @FromNum,
            @ToNum,
            @Name
    END
 
CLOSE        curWork
DEALLOCATE    curWork
 
SELECT        Chromosome,
        FromNum,
        ToNum,
        Names,
        Items
FROM        #Work
--WHERE        Items > 1    -- Uncomment this line to get only the overlapping ranges
ORDER BY    FromNum
 
DROP TABLE #Work
print 'Time elapsed (sec): ' + convert(varchar(30),datediff(second, @TimeStart, getdate()))
```

Set based solution based on the quirky update idea – it takes ~5 second to execute:

```sql
set statistics time off
set nocount on
declare @TimeStart datetime = getdate()
SELECT	Name,
	Chromosome,
	iStart,
	iEnd,
	0 AS Grp
INTO	#Temp
FROM	dbo.Chromosomes

DECLARE	@Grp INT = 0,
	@Chromosome VARCHAR(10) = '',
	@End INT = 0

;WITH cteUpdate(Chromosome, iStart, iEnd, Grp)
AS (
	SELECT		TOP(2147483647)
			Chromosome,
			iStart,
			iEnd,
			Grp
	FROM		#Temp
	ORDER BY	Chromosome,
			iStart
)
-- Quirky update - updating variable and field at the same time
UPDATE	cteUpdate
SET	@Grp = Grp =	CASE
				WHEN Chromosome <> @Chromosome THEN @Grp + 1
				WHEN @End < iStart THEN @Grp + 1
				ELSE @Grp
			END,
	@End =	CASE
			WHEN Chromosome <> @Chromosome THEN iEnd
			WHEN iEnd < @End THEN @End
			ELSE iEnd
		END,
	@Chromosome = Chromosome

SELECT		Chromosome,
		MIN(iStart) AS FromNum,
		MAX(iEnd) AS ToNum,
		CAST(MIN(Name) AS VARCHAR(MAX)) AS Names,
		COUNT(*) AS Items,
		Grp
INTO		#Stage
FROM		#Temp
GROUP BY	Chromosome,
		Grp

UPDATE		s
SET		s.Names += f.Names
FROM		#Stage AS s
CROSS APPLY	(
			SELECT DISTINCT	', ' + x.Name
			FROM		#Temp AS x
			WHERE		x.Grp = s.Grp
					AND x.Name > s.Names
			FOR XML		PATH('')
		) AS f(Names)
WHERE		s.Items > 1

SELECT		Chromosome,
		FromNum,
		ToNum,
		Names,
		Items
FROM		#Stage
ORDER BY	Chromosome,
		FromNum

DROP TABLE	#Temp,
		#Stage
		
print 'Time elapsed (sec): ' + convert(varchar(30),datediff(second, @TimeStart, getdate()))
```

&nbsp;

# Third problem – Transpose columns to rows

The third problem is simple enough, but yet quite interesting. It is presented in the following [Vertical Result Set thread][4]

Transpose table of any structure vertically (in other words, each column becomes a row) and show just a few typical data for every column.

First problem is to convert the data into the same format. I chose nvarchar(max), but it may not be 100% working solution if there are columns with exotic data types that can not be converted to nvarchax(max). In this case we may want to use sql_variant as the type instead.

Here is the solution I used based on AdventureWorks.Person.Address table:

<pre><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">declare</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">@</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">SQL nvarchar</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">(</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">max</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">);

</span><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">select</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">@</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">SQL </span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">=</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000"> STUFF</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">((</span><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">SELECT</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">' UNION ALL
SELECT AddressID, convert(nvarchar(max),'</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">+</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000"> quotename</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">(</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">column_name</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">)</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">+</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">') as Column_Value, '</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">+</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">
QUOTENAME</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">(</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">Column_Name</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">,</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">''''</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">)</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">+</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">' as Column_Name FROM AdventureWorks.Person.Address'
</span><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">from</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000"> AdventureWorks</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">.</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">INFORMATION_SCHEMA</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">.</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">COLUMNS 
</span><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">where</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000"> TABLE_NAME </span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">=</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">'Address'</span><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">and</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000"> TABLE_SCHEMA </span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">=</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">'Person'</span><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">FOR</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000"> XML PATH</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">(</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">''</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">),</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000"> type</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">).</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">value</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">(</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">'.'</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">,</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">'nvarchar(max)'</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">),</span><span class="lit" style="font-weight: inherit;font-style: inherit;color: #006666">1</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">,</span><span class="lit" style="font-weight: inherit;font-style: inherit;color: #006666">11</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">,</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">''</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">)

</span><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">print  </span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">@</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">SQL


</span><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">set</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">@</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">SQL </span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">=</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">@</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">SQL </span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">+</span><span class="str" style="font-weight: inherit;font-style: inherit;color: #008800">'
ORDER BY AddressId'
</span><span class="kwd" style="font-weight: inherit;font-style: inherit;color: #000088">execute</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">(@</span><span class="pln" style="font-weight: inherit;font-style: inherit;color: #000000">SQL</span><span class="pun" style="font-weight: inherit;font-style: inherit;color: #666600">)</span></pre>

And here is Hunchback's (Alejandro Mesa) solution using SQL Server 2008 specific syntax:

```sql
SELECT
	C.*
FROM
	(
 SELECT TOP (3)
	 AddressID,
	 AddressLine1,
	 AddressLine2,
	 City,
	 StateProvinceID,
	 PostalCode
 FROM
	 Person.Address
 ORDER BY
  ModifiedDate
 ) AS T
 CROSS APPLY
 (
 VALUES
  (AddressID, 'AddressID', CAST(AddressID AS sql_variant)),
  (AddressID, 'AddressLine1', CAST(AddressLine1 AS sql_variant)),
  (AddressID, 'AddressLine2', CAST(AddressLine2 AS sql_variant)),
  (AddressID, 'City', CAST(City AS sql_variant)),
  (AddressID, 'StateProvinceID', CAST(StateProvinceID AS sql_variant)),
  (AddressID, 'PostalCode', CAST(PostalCode AS sql_variant))
 ) AS C(rowident, cn, cv)
ORDER BY
 rowident,
 cn;
GO
```

&nbsp;

## Fourth problem – Count % of NULLs in every column in a table

I decided to add this problem here rather than create a new blog for the last week interesting problems.
  
The problem is presented in [this thread][5]. For any given table find percent of NULL values in any column and post the result.

My solution for this problem is to create dynamic query using the idea from my other blog [How to get information about all databases without a loop][6]:

```sql
USE AdventureWorks 

DECLARE  @TotalCount DECIMAL(10,2), 
         @SQL        NVARCHAR(MAX) 

SELECT @TotalCount = COUNT(* ) 
FROM   [AdventureWorks].[Production].[Product] 

SELECT @SQL = COALESCE(@SQL + ', ','SELECT ') + CASE 
                                                  WHEN IS_NULLABLE = 'NO' THEN '0' 
                                                  ELSE 'cast(sum (case when ' + QUOTENAME(column_Name) + ' IS NULL then 1 else 0 end)/@TotalCount*100.00 as decimal(10,2)) '
                                                END + ' as [' + column_Name + ' NULL %] ' 
FROM   INFORMATION_SCHEMA.COLUMNS 
WHERE  TABLE_NAME = 'Product' 
       AND TABLE_SCHEMA = 'Production' 

SET @SQL = 'set @TotalCount = NULLIF(@TotalCount,0)  ' + @SQL + ' FROM [AdventureWorks].Production.Product' 

--print @SQL 
EXECUTE SP_EXECUTESQL 
  @SQL , 
  N'@TotalCount decimal(10,2)' , 
  @TotalCount
```

Hope you find these problems interesting as well and see you in a week (or more)...

The next series of this blog:

[Interesting T-SQL problems – new problems][7]

And perhaps you appreciate this topic as well
  
[How to search a string in all tables in a database][8]

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][9] forum or our [Microsoft SQL Server Admin][10] forum**<ins></ins>

 [1]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/7cae5f7a-7f83-498f-9199-1a6379c64744
 [2]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/87d2b1d5-ee1e-4906-a499-3e38775614d3
 [3]: http://social.msdn.microsoft.com/Forums/en/transactsql/thread/fe0162ed-9514-4af8-a113-3eb54d349924
 [4]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/1ab220c0-1b10-4cfc-8f50-4417c4fc7143
 [5]: http://social.msdn.microsoft.com/Forums/en/sqlexpress/thread/50b4f688-e294-434b-aa6f-e9fd1f64fa5f
 [6]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas
 [7]: http://beyondrelational.com/blogs/naomi/archive/2010/10/17/interesting-t-sql-problems.aspx
 [8]: http://beyondrelational.com/blogs/naomi/archive/2010/10/29/how-to-search-a-string-value-in-all-columns-in-the-table-and-in-all-tables-in-the-database.aspx
 [9]: http://forum.lessthandot.com/viewforum.php?f=17
 [10]: http://forum.lessthandot.com/viewforum.php?f=22