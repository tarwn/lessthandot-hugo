---
title: Why every database should have a calendar table
author: SQLDenis
type: post
date: -001-11-30T00:00:00+00:00
ID: 888
excerpt: |
  create table Calendar (
          CalendarID smallint not null primary key,
  		DateStamp date not null,
  		DayOfYear as DATEPART(dy,DateStamp),
  		WeekNumber as DATEPART(wk,DateStamp),
  		MonthNumber as DATEPART(mm,DateStamp),
  		QuarterNumber as DATEPAR&hellip;
draft: true
url: /?p=888
categories:
  - Database Programming
  - Microsoft SQL Server

---
```sql
create table Calendar (
        CalendarID smallint not null primary key,
		DateStamp date not null,
		DayOfYear as DATEPART(dy,DateStamp),
		WeekNumber as DATEPART(wk,DateStamp),
		MonthNumber as DATEPART(mm,DateStamp),
		QuarterNumber as DATEPART(qq,DateStamp),
		YearNumber as YEAR(DateStamp),
		IsWeekend as CASE WHEN DATEPART(dw,DateStamp) IN(1,7) then 1 else 0 end,
		IsHoliday tinyint)
GO
```

```sql
declare @d date
select @d = '20100101'				
	
insert Calendar(CalendarID,DateStamp,IsHoliday)					
select	number as CalendarID,
		DATEADD(DD,number,@d)	as DateStamp,
		0 as IsHoliday
from (				
select number from master..spt_values
where type = 'P'
union all
select number + 2048 from master..spt_values
where type = 'P') x
```

```sql
create index ix_cal on Calendar(DateStamp,CalendarID)
```

```sql
select *
 from Calendar
where DateStamp >= '20100826'

select *
 from Calendar
where CalendarID >= 237
```

```sql
select MIN(DateStamp),MAX(DateStamp)
from Calendar
```
