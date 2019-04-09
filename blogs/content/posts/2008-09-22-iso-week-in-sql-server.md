---
title: ISO Week In SQL Server
author: SQLDenis
type: post
date: 2008-09-22T14:03:04+00:00
ID: 149
url: /index.php/datamgmt/datadesign/iso-week-in-sql-server/
views:
  - 47571
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - dates
  - functions
  - how to
  - iso
  - iso week
  - sql server 2008
  - tip

---
### ISO Week in SQL Server

First let's take a look at what ISO week is, from WikiPedia:
  
Week date representations are in the format as shown below.
  
YYYY-Www or YYYYWww
  
YYYY-Www-D or YYYYWwwD

[YYYY] indicates the so-called ISO year which is slightly different than the calendar year (see below). [Www] is the week number prefixed by the letter &#8216;W', from W01 through W53. [D] is the weekday number, from 1 through 7, beginning with Monday and ending with Sunday. This form is popular in the manufacturing industries.
  
There are mutually equivalent definitions for week 01:

  * the week with the year's first Thursday in it,
  * the week with 4 January in it,
  * the first week with the majority (four or more) of its days in the starting year, and
  * the week starting with the Monday in the period 29 December – 4 January.

If 1 January is on a Monday, Tuesday, Wednesday or Thursday, it is in week 01. If 1 January is on a Friday, Saturday or Sunday, it is in week 52 or 53 of the previous year.
  
The week number can be described by counting the Thursdays: week 12 contains the 12th Thursday of the year.
  
The ISO year starts at the first day (Monday) of week 01 and ends at the Sunday before the new ISO year (hence without overlap or gap). It consists of 52 or 53 full weeks. The ISO year number deviates from the number of the calendar year (Gregorian year) on a Friday, Saturday, and Sunday, or a Saturday and Sunday, or just a Sunday, at the start of the calendar year (which are at the end of the previous ISO year) and a Monday, Tuesday and Wednesday, or a Monday and Tuesday, or just a Monday, at the end of the calendar year (which are in week 01 of the next ISO year). For Thursdays, the ISO year number is always equal to the calendar year number.
  
Examples:

  * 2008-12-29 is written “2009-W01-1”
  * 2010-01-03 is written “2009-W53-7”

You can read more about ISO week here: http://en.wikipedia.org/wiki/ISO\_week\_date

Sometimes you need to show the ISO week in SQL server but there was no built in way to calculate it until SQL server 2008 was released. In SQL Server 2000/2005 you could use the user defined function ISOweek which was in the SQL Server books on line.
  
Here is what the function looks like

sql
CREATE FUNCTION ISOweek  (@DATE datetime)
RETURNS int
AS
BEGIN
   DECLARE @ISOweek int
   SET @ISOweek= DATEPART(wk,@DATE)+1
      -DATEPART(wk,CAST(DATEPART(yy,@DATE) as CHAR(4))+'0104')
--Special cases: Jan 1-3 may belong to the previous year
   IF (@ISOweek=0) 
      SET @ISOweek=dbo.ISOweek(CAST(DATEPART(yy,@DATE)-1 
         AS CHAR(4))+'12'+ CAST(24+DATEPART(DAY,@DATE) AS CHAR(2)))+1
--Special case: Dec 29-31 may belong to the next year
   IF ((DATEPART(mm,@DATE)=12) AND 
      ((DATEPART(dd,@DATE)-DATEPART(dw,@DATE))>= 28))
      SET @ISOweek=1
   RETURN(@ISOweek)
END
GO
```

Now run the following query on SQL server 2000 and up

sql
select dbo.ISOweek('20071231'),datepart(wk,'20071231')
```

If you are running SQL server 2008 then you can use DATEPART and the datepart argument isowk. Run the select statement below to see the result

sql
select datepart(isowk,'20071231'),datepart(wk,'20071231')
```

As you can see here also SQL Server's wk part returns 53 while isowk returns 1

I have also added parts of this to the wiki here: [ISO Week In SQL Server][1]

 [1]: http://wiki.ltd.local/index.php/ISO_Week_In_SQL_Server