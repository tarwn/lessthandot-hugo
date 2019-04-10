---
title: Adding time offsets passed in to a datetime to generate localized datetime
author: SQLDenis
type: post
date: 2009-06-26T16:44:46+00:00
ID: 485
url: /index.php/datamgmt/datadesign/adding-time-offsets-passed-in-to-a-datet/
views:
  - 5064
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - dates
  - datetime
  - parsing
  - time
  - utc time

---
I answered this question today and thought it would be useful to create a little post

If you have a varchar value like this '2009-06-26 14:30:00.000Z+4:30' you want to take 4 hours and 30 minutes and then subtract that from the date itself so in this case you will get 2009-06-26 11:00:00.000. The reason you subtract is because the +4:30 means that this was generated in a zone that is 4:30 ahead of utc

So first we need to figure out a couple of things
  
1) where are the minutes?
  
2) where is the hour?
  
3) is it positive or negative?

Here are the answers
  
**1) where are the minutes?**
  
The minutes are the last 2 characters

sql
declare @date varchar(100)
select @date = '2009-06-26 14:30:00.000Z+4:30'

select right(@date,2)
```

**2) where is the hour?**
  
The hour starts after the Z and last for 2 or 3 characters including the sign, we will just grab 3 and replace : with an empty string

sql
declare @date varchar(100)
select @date = '2009-06-26 14:30:00.000Z+4:30'
select replace(substring(@date,patindex('%z%',@date)+ 1,3),':','')
go
```

+4

sql
declare @date varchar(100)
select @date = '2009-06-26 14:30:00.000Z-4:30'
select replace(substring(@date,patindex('%z%',@date)+ 1,3),':','')
go
```

-4

sql
declare @date varchar(100)
select @date = '2009-06-26 14:30:00.000Z+14:30'
select replace(substring(@date,patindex('%z%',@date)+ 1,3),':','')
```

+14

**3) is it positive or negative?**
  
That we already grabbed above for the hour, for the minute we need to do something like this

sql
declare @date varchar(100),@multiplier int

select @date = '2009-06-26 14:30:00.000Z+4:30'
select  case when @date like '%+%' then -1 else 1 end
```

We also need to convert the stuff we did above to integers in order to add

So here is the complete code

sql
declare @date varchar(100),@multiplier int

select @date = '2009-06-26 14:30:00.000Z+4:30'
select @multiplier = case when @date like '%+%' then -1 else 1 end


select dateadd(mi, @multiplier *convert(int,right(@date,2)),dateadd(hh
    ,-1 * convert(int,replace(substring(@date,patindex('%z%',@date)+ 1,3),':',''))
    ,left(@date,23)))
go


--2009-06-26 10:00:00.000

declare @date varchar(100),@multiplier int

select @date = '2009-06-26 14:30:00.000Z-4:30'
select @multiplier = case when @date like '%+%' then -1 else 1 end

select dateadd(mi, @multiplier *convert(int,right(@date,2)),dateadd(hh
    ,-1 * convert(int,replace(substring(@date,patindex('%z%',@date)+ 1,3),':',''))
    ,left(@date,23)))
go

--2009-06-26 19:00:00.000

declare @date varchar(100),@multiplier int

select @date = '2009-06-26 14:30:00.000Z+14:30'
select @multiplier = case when @date like '%+%' then -1 else 1 end

select dateadd(mi, @multiplier *convert(int,right(@date,2)),dateadd(hh
    ,-1 * convert(int,replace(substring(@date,patindex('%z%',@date)+ 1,3),':',''))
    ,left(@date,23)))
go

--2009-06-26 01:00:00.000


declare @date varchar(100),@multiplier int
select @date = '2009-06-26 14:30:00.000Z-14:30'
select @multiplier = case when @date like '%+%' then -1 else 1 end

select dateadd(mi, @multiplier *convert(int,right(@date,2)),dateadd(hh
    ,-1 * convert(int,replace(substring(@date,patindex('%z%',@date)+ 1,3),':',''))
    ,left(@date,23)))
go

--2009-06-27 05:00:00.000
```


\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22