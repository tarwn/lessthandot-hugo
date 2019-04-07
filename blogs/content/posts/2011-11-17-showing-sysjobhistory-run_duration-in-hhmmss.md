---
title: Showing sysjobhistory run_duration in HHMMSS format
author: SQLDenis
type: post
date: 2011-11-17T23:27:00+00:00
ID: 1388
excerpt: |
  Someone asked the following question on our forum: HHMMSS format in sysjobhistory.
  
  The sysjobhistory table in MSDB stores the run time of jobs in the format HHMMSS as an integer. Thus, a job that finishes in 18 minutes and 9 seconds is stored as 1809&hellip;
url: /index.php/datamgmt/datadesign/showing-sysjobhistory-run_duration-in-hhmmss/
views:
  - 66629
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server

---
Someone asked the following question on our forum: [HHMMSS format in sysjobhistory][1].

> The sysjobhistory table in MSDB stores the run time of jobs in the format HHMMSS as an integer. Thus, a job that finishes in 18 minutes and 9 seconds is stored as 1809. If a job finishes in 4 seconds it&#8217;s stored as 4, and another that finishes in 7 hours, 21 minutes, 33 seconds as 72133. I&#8217;m trying to get to this data and need it formated in the time data type. Thus I&#8217;m looking for 00:18:09, 00:00:04, and 07:21:33 respectively.

This is pretty simple to do in a 2 step process

Step 1 is to make sure that you always have 6 characters, you can do that by using the right functions and padding the total characters to 6, here is an example

<pre>RIGHT('000000' + CONVERT(VARCHAR(6),run_duration),6 )</pre>

Once you have that, you can use the STUFF function to inject colons in position 3 and 6, the code would look like this

<pre>STUFF(STUFF(Value,3,0,':'),6,0,':')</pre>

The 0 means that you don&#8217;t want to replace any characters

So to put it all together, you can use a CTE (Common Table Expression) combined with the stuff function to accomplish this job

Here is all the code that is needed

sql
;WITH cte AS(SELECT RIGHT('000000' + CONVERT(VARCHAR(6),run_duration),6 ) AS FormattedTime,* FROM msdb..sysjobhistory
WHERE run_duration >=0)
 
SELECT STUFF(STUFF(FormattedTime,3,0,':'),6,0,':'),* FROM cte
```

Now I don&#8217;t have any jobs that run over 24 hours, I might have some of them that are 16 hours or so I am not sure if it adds to the hours or if it starts to add 2 more digits for the days

If you have never used the STUFF function before make sure to read our wiki: [Ten SQL Server Functions That You Have Ignored Until Now][2] to learn about other seldom used functions like
  
BINARY_CHECKSUM
  
SIGN
  
COLUMNPROPERTY
  
DATALENGTH
  
ASCII, CHAR,UNICODE
  
NULLIF
  
PARSENAME
  
STUFF
  
REVERSE
  
GETUTCDATE

Have fun

 [1]: http://forum.ltd.local/viewtopic.php?f=22&t=15776
 [2]: http://wiki.ltd.local/index.php/Ten_SQL_Server_Functions_That_You_Have_Ignored_Until_Now