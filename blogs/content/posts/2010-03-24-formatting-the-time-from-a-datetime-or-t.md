---
title: Formatting the time from a datetime or time datatype by using the STUFF function
author: SQLDenis
type: post
date: 2010-03-24T18:26:11+00:00
ID: 734
url: /index.php/datamgmt/dbprogramming/formatting-the-time-from-a-datetime-or-t/
views:
  - 10125
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - function
  - sql server 2005
  - sql server 2008
  - stuff
  - tip

---
This is a quick post about a question that had to do with time formatting. This question was asked in our [SQL Server programming forum][1] today

> I don't see a format of hh:mm AM/PM only 
> 
> Do we need to do it using datepart or how to get only hour:minute am/pm format ?

The question can be found here: [Formatting time only][2]. SQL Server has a bunch of formats for converting datetime values, a list of all of them can be found in Books On Line here: [CAST and CONVERT (Transact-SQL)][3]

So let's say for example that you have this datetime &#8216;2010-03-24 16:20:01.800' and you want to show 4:20 PM, how can you do that?

Let's take a look, if you are on SQL Server 2008 you can convert to time

sql
DECLARE @t TIME = '2010-03-24 16:20:01.800'
SELECT @t AS TIME
```

&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
16:20:01.8000000

Okay that gives me 16:20:01.8000000, not what the original poster wanted

You can do this

sql
DECLARE @t TIME = '2010-03-24 16:20:01.800'
SELECT CONVERT(VARCHAR(30),@t,100)
```

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
4:20PM

That gives me 4:20PM. But how can I add a space between 0 and PM? Say hello to my little friend [STUFF][4]. In order to add a space you need to start at position 5 and add a space and don't do any replacement.

Take a look at what I mean

sql
SELECT STUFF('4:20PM',5,0,' ')
```

&#8212;&#8212;&#8212;&#8212;&#8212;
  
4:20 PM

Now let's use the code from before and wrap the STUFF function around it

sql
DECLARE @t TIME = '2010-03-24 16:20:01.800'
SELECT STUFF(RIGHT(' ' + CONVERT(VARCHAR(30),@t,100),7),6,0,' ') AS FormattedTime
```
&#8212;&#8212;&#8212;&#8212;&#8211;
  
4:20 PM

Okay but what if you are not on SQL Server 2008? No problem really, it is almost the same

sql
DECLARE @d datetime 
SET @d = '2010-03-24 16:20:01.800'
SELECT STUFF(RIGHT(' ' + CONVERT(VARCHAR(30),@d,100),7),6,0,' ')
```

&#8212;&#8212;&#8212;&#8212;&#8211;
  
4:20 PM

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][5] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewtopic.php?f=17&t=10289
 [3]: http://msdn.microsoft.com/en-us/library/ms187928.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms188043.aspx
 [5]: http://forum.ltd.local/viewforum.php?f=22