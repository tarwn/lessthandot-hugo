---
title: Calculating Nth weekday in a month
author: Ramireddy
type: post
date: 2010-03-02T16:32:30+00:00
ID: 717
url: /index.php/datamgmt/dbprogramming/calculating-nth-weekday-in-a-month/
views:
  - 7949
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server

---
How to calculate the Nth week day of a month?

This will be useful in most of the Scheduling problems like Scheduling Jobs, Appointments etc. 

Let me demonstrate how we can achieve this:

Let us take an example of say, 3rd Sunday in February, 2010 will come on 21st February, 2010.
  
The solution to the above problem needs to satisfy 3 conditions.
  
1. The date should be in the specified month and year.
  
2. The date should be in the specified week number.
  
3. The date should be of the specified week day.
  
Solving this problem comprises the following 3 steps, which logically satisfy each of the above conditions:
  
1. Get the first date for the provided month and year. E.g., the first date for the specified month (February) and Year (2010) is February 1st 2010.The following query will give you the first date for provided month and year.

```sql
SELECT DATEADD(MONTH,@Month-1,DATEADD(YEAR,@Year-1900,0))
```
2. Get the first date on which any weekday will occur nth time in the month. i.e. ((N-1)\*7+1)th day of the month will be the first date on which any weekday will occur nth time. In this case, the first date for the specified week (3) is, February 1st 2010 + (3 – 1)\*7 days = February 15th 2010.

```sql
SELECT DATEADD(DAY,(@Weekno-1)*7 ,DATEADD(MONTH,@Month-1,DATEADD(YEAR,@Year-  1900,0)))
```
3. Now add the number of days required to calculate the specified weekday. This will depend on the weekday specified and the weekday of the first date that came in the above step. These will be based on the weekday specified and the weekday of the first date that came in above step.
  
Week day Requested is 6 (Sunday) and Weekday of first date that came in above step is 0(Monday).
  
So, No of days required to add are (Weekday Requested – weekday of first date ) = 6 – 0 = 6 days.
  
Note: If the above formula results in negative value, add 7 to the result.
  
You can write the above expression as
  
No of days to add = (7 + Weekday Requested – Weekday Of first date) % 7
  
In our case, the number of days required to add are 6 – 0 = 6 days. So, by adding 6 days to 15th February 2010,you will get 21st February 2010, which is 3rd Sunday of February 2010.
  
So, the final query will become,

```sql
SELECT DATEADD(DAY,((7+@Weekday)-DATEDIFF(dd,0,t.mydate)%7)%7,t.mydate) FROM 
	    (SELECT DATEADD(DAY,(@Weekno-1)*7 ,DATEADD(MONTH,@Month- 
	1,DATEADD(YEAR,@Year-1900,0))) AS mydate )t)

```
Finally, the function written below is the complete solution that will be useful in calculating Nth weekday of any given month and year.

```sql
CREATE FUNCTION fn_getWeekDay (@YEAR INT,@MONTH INT,@WeekNo INT,@WeekDay INT) 
	RETURNS DATETIME 
	AS 
	BEGIN 
	    RETURN (   
	    SELECT DATEADD(DAY,((7+@Weekday)-DATEDIFF(dd,0,t.mydate)%7)%7,t.mydate) FROM 
	    (SELECT DATEADD(DAY,(@Weekno-1)*7 ,DATEADD(MONTH,@Month-1,DATEADD(YEAR,@Year-1900,0))) AS mydate )t) 
	END
```
The above function takes 4 parameters: year, month, week number and weekday (Monday-0, Tuesday-1, Wednesday-2, Thursday-3, Friday-4, Saturday-5, Sunday-6)
  
So, in order to calculate 3rd Sunday in February 20010, you need to call the function like below.

```sql
SELECT dbo.fn_getWeekDay(2010,2,2,3)
```
I hope my function can be useful for you and see you in my next blog.