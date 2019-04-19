---
title: Calculating number of workdays between 2 dates
author: Ramireddy
type: post
date: 2010-02-25T18:09:17+00:00
ID: 712
url: /index.php/datamgmt/dbprogramming/calculating-number-of-workdays-between-2/
views:
  - 25782
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
Today I saw someone asked a question in MSDN t-sql forums [“How to calculate the number of working days between two given dates”.][1] I found this very interesting, since we can solve this problem by using a very simple and methodical approach and by using an unorthodox approach. I will explain both the approaches.

**1. By Using Auxilary Calendar Table
  
2. Based on the weekdays of start date and end dates.
  
** 

**By Using Auxilary Calendar Table :** 
             
This is a good and methodical approach. In this approach, the database has a table that has all the dates that we can represent using sql server. The following script will insert all the dates between 1/1/1753 (minimum date sql can recognize in sql) and 12/31/9999 (maximum date sql can recognize in sql).

```sql
create table AuxCalendarDates
(
	CalDate datetime
)

;with N as
(
	select 0 as num union all select 1 union all select 2 union all select 3 union all select 4 union all select 5 union all
	select 6 union all select 7 union all select 8 union all select 9	
),
Numbers as
(
	select row_number() over (order by (select null)) as Number from N N,N N1,N N2,N N3,N N4,N N5,N N6
)
insert into AuxCalendarDates
select dateadd(day,Number - 1,'1/1/1753') from Numbers where Number <= 3012154

```
3012154 is the Total number of days sql can recognize.

Once, calendar table created, Solving this is fairly straight forward. It needs to satisfy the following 2 conditions.

1. The Calendar date should between the 2 given dates. using a where clause like below will do the trick.

```sql
where caldate between @startdate and @enddate 
   
```

2. date should be between Monday to Friday
       
To find the weekday, the normal approach is using datepart(dw,date) function.
  
But unfortunately this function depends on the @@datefirst settings.
  
If we change the datefirst value, we will get an different week day. run the below code in SSMS. It will give different week numbers for different datefirst values.

```sql
set datefirst 7
select datepart(dw,getdate())
set datefirst 5
select datepart(dw,getdate())
```
There is another way to find a weekday,
  
Calculating the number of days since the beginning and calculating the remainder by dividing with 7.

```sql
select datediff(dd,0,getdate())%7
```
The above expression will give 0 for Monday, 1 for Tuesday, 2 for Wednesday , 3 for Thursday ,4 for Friday , 5 for Saturday and 6 for Sunday.

The above expression is also independent of datefirst settings.

Now using the above expression, to check the weekday, 

```sql
datediff(dd,0,Caldate)%7 between 0 and 4
```
finally, keeping it in a function, will make this re-usable.

```sql
create function [dbo].[fn_NoofWorkdaysBetweenDates_Table]
(
	@StartDate datetime,
	@EndDate datetime
)
RETURNS INT
as
BEGIN
RETURN
(
	select count(*) from AuxCalendarDates where CalDate between @startdate and @enddate
	and datediff(dd,0,Caldate)%7 between 0 and 4
);
END

```
Now i will explain the second method, which is unorthodox. This Method depends on the pattern of week day of start date and week day of end date, and based on that finding the condition.

For example, take 2 dates Feb1st2010,Feb8th2010. Feb1st 2010 is monday and Feb8th 2010 is monday. so the no of working days = 1 + ( 5 * 1) = 6 days..
   
if starting day is monday, and endday is also monday, no of working days = 1 Working day + (5 * no of finished weeks between the 2 dates)

Take another example.
     
Take 2 dates Feb1st2010,Feb9th2010. Feb1st 2010 is monday and Feb9th 2010 is tuesday. so the no of working days = 2 + ( 5 * 1) = 7 days..

if starting day is monday , and endday is tuesday, no of working days
  
= 2 working days + ( 5 * no of weeks between the 2 dates) 

Generalizing the above examples, the following formula can be derived.

No of working dates between 2 dates = (Additional Working days) + (5 * no of finished weeks between the 2 dates)

In above formula, Additional Working days will varies and it depends on the week day of starting date and weekday of ending date.

To get the Additional working days, below table will be useful..

0 1 2 3 4 5 6
   
——————
  
0| 1 2 3 4 5 5 5
  
1| 5 1 2 3 4 4 4
  
2| 4 5 1 2 3 3 3
  
3| 3 4 5 1 2 2 2
  
4| 2 3 4 4 1 1 1
  
5| 1 2 3 4 5 0 0
  
6| 1 2 3 4 5 5 0 

0 to 6 in row represents the week day of starting date
  
0 to 6 in columns represent the week day of ending date

Suppose to calculate Additional working days between Tuesday and Friday,

As, Tuesday, week day no is 1 and Friday Week day no is 4, checking the value in RowNo-1 against colNo-4 will give you the no of additional working days as 4.

Now, to use this table in query, concatenate these all columns into single row, so, that there will be only 7 rows,and use those in the query as inline table.. based on the weekday of starting date, filtering the row and based on weekday of ending date, filtering the column..
  
so, final implementation of this function will be

```sql
Create function [dbo].[fn_NoofWorkdaysBetweenDates]
(
	@StartDate datetime,
	@EndDate datetime
)
RETURNS INT
as
BEGIN
RETURN
(
	select (datediff(dd,@startdate,@enddate)/7)*5 +  substring(EndWk,datediff(dd,0,@enddate)%7+1,1)
	from ( select 0 as StartWk,'1234555' as EndWK union all select 1,'5123444' union all select 2,'4512333' union all 
	select 3,'3451222' union all select 4,'2344111' union all select 5,'1234500' union all select 6,'1234550')t
	where StartWk = datediff(dd,0,@startdate)%7
);
END

```

 [1]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/479b2888-f228-4154-9595-4c9e9b7a5523