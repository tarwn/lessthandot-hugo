---
title: Find Out What Percent Of The Log Is Being Used For Each Database In SQL Server 2005 and 2008
author: SQLDenis
type: post
date: 2010-01-13T15:38:54+00:00
url: /index.php/datamgmt/dbprogramming/find-out-what-percent-of-the-log-is-bein-2008/
views:
  - 6760
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - dmv
  - dynamic management views
  - sql server 2005
  - sql server 2008

---
Sometimes you want to quickly see the percentage of log spaced that each database is using on your server. In SQL Server 2005 and 2008 you can use the sys.dm\_os\_performance\_counters dynamic management view to find out this information. The query below will list all database and the percentage of log spaced used. The cntr\_value column will have the percent of the log file that is being used and instance_name will be the database name.

<pre>select instance_name,cntr_value from sys.dm_os_performance_counters
where Object_name = 'SQLServer:Databases'   
and counter_name = 'Percent Log Used' 
and instance_name &lt;&gt; '_Total'                                                                                                                          
order by   cntr_value desc  </pre>

Here is the output from that query.

<pre>&lt;strong&gt;instance_name       cntr_value&lt;/strong&gt;
model		    90
ReportServerTempDB  66
master              65
mssqlsystemresource 55
ReportServer        55
tempdb              50
msdb                5</pre>

Notice that I filtered out the total with this clause _and instance_name <> &#8216;_Total&#8217;_ The total number doesn&#8217;t really make sense for that query. 

Now let&#8217;s take a look at another query. What if I want to know the size in KB for each log size and also for all of them combined? Here is the query for that.

<pre>select instance_name,cntr_value from sys.dm_os_performance_counters
where Object_name = 'SQLServer:Databases'   
and counter_name = 'Log File(s) Size (KB)'   
order by   cntr_value desc </pre>

Here is the output from that query.

<pre>&lt;strong&gt;instance_name		cntr_value&lt;/strong&gt;
_Total			37524936
iSource_Report		14539576
iSource_Distribution	13217784
DJHFI_Research_db	8207096
msdb			625784
tempdb			102136
master			2808
ReportServer		1016
model			1016
ReportServerTempDB	760
mssqlsystemresource	504</pre>

As you can see _Total is the first thing listed and it is actually a sum of all the log files in the query. The numbers don&#8217;t add up in my output because I removed some database names after running the query.

Since I showed you how to do the log files, here is a way how to show the size of all the data files. here is the query for the data files

<pre>select instance_name,cntr_value from sys.dm_os_performance_counters
where Object_name = 'SQLServer:Databases'   
and counter_name = 'Data File(s) Size (KB)'   
order by   cntr_value desc </pre>

And here is the output

<pre>&lt;strong&gt;instance_name		cntr_value&lt;/strong&gt;
_Total			58232704
msdb                    13512512
tempdb                  1542720
mssqlsystemresource     39232
master                  6720
ReportServer            4288
ReportServerTempDB      3264
model                   2240</pre>

And just as with the log size query, you can see _Total is the first thing listed and it is actually a sum of all the data files in the query. The numbers don&#8217;t add up in my output because I removed some database names after running the query.

I will be back with another post tomorrow showing you how you can use the sys.dm\_os\_performance_counters dynamic management view to see if you are still using any deprecated features in your database.

\*** **Remember, if you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22