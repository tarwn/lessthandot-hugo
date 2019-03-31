---
title: Converting Columns To Date From Datetime Does Not Result In A Scan In SQL Server 2008
author: SQLDenis
type: post
date: 2008-07-24T16:28:16+00:00
url: /index.php/datamgmt/datadesign/converting-columns-to-date-from-datetime-2008/
views:
  - 6748
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
I was reading Itzik Ben-Gan&#8217;s An Introduction to [New T-SQL Programmability Features in SQL Server 2008][1] article yesterday after one of my friends allerted me to the following from that article
  
For example, the plan for the following query performs an index seek on the index on the CurrencyRateDate DATETIME column:

<pre>USE AdventureWorks;

SELECT FromCurrencyCode, ToCurrencyCode, EndOfDayRate

FROM Sales.CurrencyRate

WHERE CAST(CurrencyRateDate AS DATE) = '20040701';</pre>

I was surprised by this, as we all know functions/conversions on column names are generaly bad for performance.

Let&#8217;s see how this works. First create this table in the tempdb database.

<pre>use tempdb

go

create table TestDatetimePerf (SomeCol datetime,id int identity)

go</pre>

This will insert 2048 rows with dates between 2008-01-01 12 AM and 2008-03-26 7 AM

<pre>insert TestDatetimePerf(SomeCol)

select dateadd(hh,number,'20080101')

from master..spt_values

where type ='P'

go

create index ix_Date on TestDatetimePerf(SomeCol)

go</pre>

Turn on the execution plan

<pre>set showplan_text on

go</pre>

Execute the following query

<pre>select * 

from TestDatetimePerf

where convert(varchar(30),SomeCol,112) = '20080103'</pre>

Here is the plan

<pre>|--Table Scan(OBJECT:([tempdb].[dbo].[TestDatetimePerf]), 
--WHERE:(CONVERT(varchar(30),[tempdb].[dbo].[TestDatetimePerf].[SomeCol],112)=[@1]))</pre>

As you can see that results in a scan. 

What happens when you convert to date?

<pre>select * 

from TestDatetimePerf

where convert(date,SomeCol) = '20080103'</pre>

Here is the plan

<pre>|--Nested Loops(Inner Join, OUTER REFERENCES:([Bmk1000]))
|--Nested Loops(Inner Join, OUTER REFERENCES:([Expr1007], [Expr1008], [Expr1006]))
| |--Compute Scalar(DEFINE:(([Expr1007],[Expr1008],[Expr1006])=GetRangeThroughConvert('2008-01-03','2008-01-03',(62))))
| | |--Constant Scan
| |--Index Seek(OBJECT:([tempdb].[dbo].[TestDatetimePerf].[ix_Date]), 
--SEEK:([tempdb].[dbo].[TestDatetimePerf].[SomeCol] &gt; [Expr1007] 
--AND [tempdb].[dbo].[TestDatetimePerf].[SomeCol] &lt; [Expr1008]), 
--WHERE:(CONVERT(date,[tempdb].[dbo].[TestDatetimePerf].[SomeCol],0)='2008-01-03') ORDERED FORWARD)
|--RID Lookup(OBJECT:([tempdb].[dbo].[TestDatetimePerf]), SEEK:([Bmk1000]=[Bmk1000]) LOOKUP ORDERED FORWARD)</pre>

See that? You get a seek instead, very interesting. It would be nice that when you use convert with the style optional parameter that the optimizer would be smart enough to convert that also to a seek.

 [1]: http://msdn.microsoft.com/en-gb/library/cc721270(SQL.100).aspx