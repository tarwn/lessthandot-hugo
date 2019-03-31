---
title: Grouping datetime values in half hour blocks in SQL Server
author: SQLDenis
type: post
date: 2009-05-29T14:38:49+00:00
url: /index.php/datamgmt/datadesign/grouping-datetime-values-in-half-hour-bl/
views:
  - 19213
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - dates
  - datetime
  - grouping
  - sql server 2000
  - sql server 2008
  - time

---
I answered this question a while back and decided to create a little blogpost.

Let&#8217;s say you have the following datetime values

2009-05-12 11:13:19.667
  
2009-05-12 11:12:19.667
  
2009-05-12 11:33:19.667
  
2009-05-12 11:43:19.667
  
2009-05-12 11:03:19.667
  
2009-05-12 11:53:19.667
  
2009-05-12 11:53:19.667
  
2009-05-12 11:23:19.667
  
2009-05-12 12:13:19.667
  
2009-05-12 12:12:19.667
  
2009-05-12 13:33:19.667
  
2009-05-12 13:43:19.667
  
2009-05-12 14:03:19.667
  
2009-05-12 14:53:19.667
  
2009-05-12 15:53:19.667
  
2009-05-12 15:23:19.667

What you want to do is break them into half hour blocks and count them, your output should look like this

vCount time
  
4 2009-05-12 11:00:00.000
  
4 2009-05-12 11:30:00.000
  
2 2009-05-12 12:00:00.000
  
2 2009-05-12 13:30:00.000
  
1 2009-05-12 14:00:00.000
  
1 2009-05-12 14:30:00.000
  
1 2009-05-12 15:00:00.000
  
1 2009-05-12 15:30:00.000

This is simple to do with a CASE statement in SQL Server. First create the following table with sample data

<pre>create table #temp (SomeDate datetime)
insert #temp values ( '2009-05-12 11:13:19.667')
insert #temp values ( '2009-05-12 11:12:19.667')
insert #temp values ( '2009-05-12 11:33:19.667')
insert #temp values ( '2009-05-12 11:43:19.667')
insert #temp values ( '2009-05-12 11:03:19.667')
insert #temp values ( '2009-05-12 11:53:19.667')
insert #temp values ( '2009-05-12 11:53:19.667')
insert #temp values ( '2009-05-12 11:23:19.667')

insert #temp values ( '2009-05-12 12:13:19.667')
insert #temp values ( '2009-05-12 12:12:19.667')
insert #temp values ( '2009-05-12 13:33:19.667')
insert #temp values ( '2009-05-12 13:43:19.667')
insert #temp values ( '2009-05-12 14:03:19.667')
insert #temp values ( '2009-05-12 14:53:19.667')
insert #temp values ( '2009-05-12 15:53:19.667')
insert #temp values ( '2009-05-12 15:23:19.667')</pre>

Here is what the select statement looks like

<pre>select count(*) as vCount,case when datepart(mi,Somedate) &lt; 30 
then dateadd(hh, datediff(hh, 0, Somedate)+0, 0)
else dateadd(mi,30,dateadd(hh, datediff(hh, 0, Somedate)+0, 0)) end as time
from #temp
group by case when datepart(mi,Somedate) &lt; 30 then dateadd(hh, datediff(hh, 0, Somedate)+0, 0)
 else dateadd(mi,30,dateadd(hh, datediff(hh, 0, Somedate)+0, 0)) end</pre>

As you can see we look at the minutes, if it is below 30 we make it 0 otherwise we make it 30.

Here is the output again

vCount time
  
4 2009-05-12 11:00:00.000
  
4 2009-05-12 11:30:00.000
  
2 2009-05-12 12:00:00.000
  
2 2009-05-12 13:30:00.000
  
1 2009-05-12 14:00:00.000
  
1 2009-05-12 14:30:00.000
  
1 2009-05-12 15:00:00.000
  
1 2009-05-12 15:30:00.000

Here is what the data looks side by side if you run the following query

<pre>select Somedate,case when datepart(mi,Somedate) &lt; 30 then dateadd(hh, datediff(hh, 0, Somedate)+0, 0)
 else dateadd(mi,30,dateadd(hh, datediff(hh, 0, Somedate)+0, 0)) end
from #temp</pre>

Somedate
  
2009-05-12 11:13:19.667 2009-05-12 11:00:00.000
  
2009-05-12 11:12:19.667 2009-05-12 11:00:00.000
  
2009-05-12 11:33:19.667 2009-05-12 11:30:00.000
  
2009-05-12 11:43:19.667 2009-05-12 11:30:00.000
  
2009-05-12 11:03:19.667 2009-05-12 11:00:00.000
  
2009-05-12 11:53:19.667 2009-05-12 11:30:00.000
  
2009-05-12 11:53:19.667 2009-05-12 11:30:00.000
  
2009-05-12 11:23:19.667 2009-05-12 11:00:00.000
  
2009-05-12 12:13:19.667 2009-05-12 12:00:00.000
  
2009-05-12 12:12:19.667 2009-05-12 12:00:00.000
  
2009-05-12 13:33:19.667 2009-05-12 13:30:00.000
  
2009-05-12 13:43:19.667 2009-05-12 13:30:00.000
  
2009-05-12 14:03:19.667 2009-05-12 14:00:00.000
  
2009-05-12 14:53:19.667 2009-05-12 14:30:00.000
  
2009-05-12 15:53:19.667 2009-05-12 15:30:00.000
  
2009-05-12 15:23:19.667 2009-05-12 15:00:00.000

To do the same for 15 minute blocks is just adding 2 more CASE statements

Here is what that looks like

<pre>select count(*) as vCount,case when datepart(mi,Somedate) &lt; 15 
then dateadd(hh, datediff(hh, 0, Somedate)+0, 0)
when datepart(mi,Somedate) between 15 and 29
then dateadd(mi,15,dateadd(hh, datediff(hh, 0, Somedate)+0, 0))
when datepart(mi,Somedate) between 30 and 44
then dateadd(mi,30,dateadd(hh, datediff(hh, 0, Somedate)+0, 0))
else dateadd(mi,45,dateadd(hh, datediff(hh, 0, Somedate)+0, 0)) end as time
from #temp
group by case when datepart(mi,Somedate) &lt; 15 
then dateadd(hh, datediff(hh, 0, Somedate)+0, 0)
when datepart(mi,Somedate) between 15 and 29
then dateadd(mi,15,dateadd(hh, datediff(hh, 0, Somedate)+0, 0))
when datepart(mi,Somedate) between 30 and 44
then dateadd(mi,30,dateadd(hh, datediff(hh, 0, Somedate)+0, 0))
else dateadd(mi,45,dateadd(hh, datediff(hh, 0, Somedate)+0, 0)) end</pre>

vCount time
  
3 2009-05-12 11:00:00.000
  
1 2009-05-12 11:15:00.000
  
2 2009-05-12 11:30:00.000
  
2 2009-05-12 11:45:00.000
  
2 2009-05-12 12:00:00.000
  
2 2009-05-12 13:30:00.000
  
1 2009-05-12 14:00:00.000
  
1 2009-05-12 14:45:00.000
  
1 2009-05-12 15:15:00.000
  
1 2009-05-12 15:45:00.000

There are other ways to skin this cat and maybe I will follow up on this next week



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22