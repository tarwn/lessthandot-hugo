---
title: 'Operand type clash: date is incompatible with int error when trying to do +1 on a date data type in SQL Server 2008'
author: SQLDenis
type: post
date: 2009-07-15T11:40:06+00:00
ID: 511
url: /index.php/datamgmt/datadesign/operand-type-clash-date-is-incompatible-2008/
aliases:
  - /index.php/datamgmt/datadesign/operand-type-clash-date-is-incompatible--2008/
views:
  - 73569
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - dates
  - gotcha
  - sql server 2008

---
I have seen enough questions about this lately and this means that it is time for a blogpost. SQL Server 2008 has a bunch of new data types and one of them is the date datatype.
  
If you don't care for the time portion of the date you can now use the date data type and save 5 bytes per row compared to datetime. I know that there is smalldatetime which only takes up 4 bytes but I myself could not use that because we have data that goes back to 1896 and thus can't be stored in smalldatetime

So take a look at this code

```sql
declare @d datetime
select @d = getdate()

select @d  =@d+1
select @d
```

To convert this to SQL Server 2008 logically you would think that all you had to do is change datetime to date. Go ahead...run it...make my day

```sql
declare @d date
select @d = getdate()

select @d  =@d+1
select @d
```

And here is the message
  
Server: Msg 206, Level 16, State 2, Line 4
  
Operand type clash: date is incompatible with int

Now what you can do is use dateadd instead

```sql
declare @d date
select @d = getdate()

select @d  = dateadd(day,1,@d)
select @d
```

So that will work with both date and datetime (should work with all the date datatypes) and it is clear what you are doing.

But wait....scroll down in the next 5 seconds and you will get another option as a bonus.

What about this?

```sql
declare @d date 
select @d = getdate() +1

select @d  
```

That does the addition before assignment.

But wait....scroll down in the next 5 seconds and you will get another option which is even shorter as a bonus.

Here is another version which is a little shorter

```sql
declare @d date = getdate() +1
select @d 
```

Even though the last two versions are shorter, I would opt for using dateadd. With dateadd you know what you are doing, what does +1 mean? Are you adding days, hours or what...it is not clear from just looking at the code without running it



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22