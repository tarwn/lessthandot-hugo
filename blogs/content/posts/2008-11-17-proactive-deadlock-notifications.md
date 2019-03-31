---
title: Proactive Deadlock Notifications
author: Ted Krueger (onpnt)
type: post
date: 2008-11-17T11:37:21+00:00
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/proactive-deadlock-notifications/
views:
  - 17699
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
Part of our jobs as DBA&#8217;s is to be proactive on performance issues that may come up. That being said there are a few things I do as a DBA by nature when I either come into an environment as a new DBA or place a new instance in the environment I&#8217;ve been maintaining for a period of time. Those things are setting a few traces on SQL Server to be default, so I know of the problems before they are serious performance issues. Today I&#8217;m going to write about the deadlock trace flags you can set and how we can use them to notify ourselves before the user community is badly affected by this critical event. 

Before I show you how to get SQL Server emailing you when there are problems like deadlock events, we should talk about the trace flags themselves. In simple terms trace flags are used to change the way SQL Server behaves. You can alter the way you start your database server, log events and configuration changes that you want to be a normal behavior that may not be an option to set permanently.

To set a trace flag I still use the DBCC command of &#8220;DBCC TRACEON&#8221;.
  
Example: 

<pre>DBCC TRACEON (3605,1204,1222,-1)</pre>

That is the manual way, but you can also set trace flags to start in the startup options of SQL Server by using the -T switch. So if I wanted 1204 to be turned on every time I restart my database server, I would use a &#8220;-T1204&#8221; in the startup options.
  
If you&#8217;re curious to see, if you&#8217;re running any trace flags, you can call a TRACESTATUS by passing in -1 as the trace flag like this

<pre>DBCC TRACESTATUS(-1)</pre>

You can also pass the trace event flag you want to see to the TRACESTATUS. Usually you won&#8217;t have many trace flags running, so it&#8217;s common to view them all. Something I should note, is some trace flags can be performance issues themselves. Trace flags that log information on every transaction or if you have a high user count and they log on each security pass will slow performance due to the nature of the logging events. Just be careful, that only the trace events you want to always be running, are set and the ones that are meant for troubleshooting real-time issues are only used periodically. 

Since we&#8217;re on it already, you should have noticed I used trace flags 3605, 1204 and 1222.
  
Explanation:
  
3605 will log what we want to the error log. We&#8217;re talk later on how to view the error log from SSMS so make life easier.
  
1204 and 1222 are the deadlock events. 1222 was introduced with 2005 and will tell you pretty much everything that is in 1204 logs. 

I like both because to be honest I&#8217;m used to reading 1204 but 1222 has proven to give me information required that 1204 does not.
  
Now you&#8217;re setup to catch deadlocks. After the traces are running it&#8217;s really just a query and a call to send the email. Every deadlock is logged as the first line of &#8220;Deadlock encountered&#8221;. Makes it pretty easy to tell if one happened. This is also why I set 1204 on. There is an xp_ command that helps you grab the error log into a result set that makes it easily queried then. That is xp_readerrorlog. Just remember you need xp enabled of course. Check surface configuration to see if it is. 

So let&#8217;s get right to the small and simple script:

<pre>If OBJECT_ID('tempdb..#ErrorLog') Is Not Null
 Begin
  Drop Table #ErrorLog
 End
Create Table #ErrorLog (logdate datetime, procInfo VarChar(10), msg VarChar(Max))
Insert Into #ErrorLog
EXEC master.dbo.xp_readerrorlog

/* check deadlock events */
If Exists (Select 1 From #ErrorLog Where msg Like '%Deadlock encountered%')
 Begin
  --email DBA deadlock event captured
   EXEC msdb.dbo.sp_send_dbmail @recipients='yourdba@yourcompany.com',
    @subject = 'Deadlock event notification',
    @body = 'Deadlock has occurred. Review ErrorLog_History immediately and take preventative actions',
    @profile_name = 'SQL DBA'

  --retain log for analysis after notification
  If OBJECT_ID('errorlog_history') Is Null
   Begin
    Create Table errorlog_history (logdate datetime, procInfo VarChar(10), msg VarChar(Max))
   End 
  Insert Into errorlog_history
  Select * from #ErrorLog
  Exec sp_cycle_errorlog
 End
 
Drop Table #ErrorLog</pre>

Some things to mention is for one the sp\_cycle\_errorlog. What this system procedure does for us is clear out the error log in SQL Server. This is important in the script above mostly, so we don&#8217;t send a thousand emails on the same event. Although if you want to have a nice reminder that there was a problem because you like to be lazy or delete emails then you can comment that out. The script then will send a notification on every run. One of the hardships here is having to log onto the server and view the log. You can grab a query and attach is to the email sent by SQL Server. Problem there is the query gets ugly due to the way the log is written. Basically you get your deadlock encountered but then just a date after that. Much easier to go in and analyze it in the history table. Notifications to me should just be that anyhow. Simple and direct telling you to get there and fix it before they hang you. 

Notice I place my error log rate into a table. This is just to make it easier for me to review the deadlock that just came up while not constantly messing with the error log. Loading the error log into a table view every time you want to see if again will of course be done on the database server and we don&#8217;t want to do that while users are working there. It&#8217;s our job to fix performance issues recall, not make more. 

There is a way with the advent of DMV&#8217;s as well to do this all as well. Problem here is it is geared towards problem child proc&#8217;s, queries etc…
  
You can see that here
  
http://weblogs.sqlteam.com/mladenp/archive/2008/05/21/SQL-Server-2005-Immediate-Deadlock-notifications.aspx

Now the warnings. Watch your errorlog size. Run sp\_cycle\_errorlog at least weekly aside from your call in the SP. If the log gets huge you&#8217;re going to cause a cascading affect down to making your tempdb grow from inserting it into the history table.
  
sp\_cycle\_errorlog here http://msdn.microsoft.com/en-us/library/ms182512.aspx

Also make sure once you&#8217;ve found a deadlock and resolved it to back the history table up for archive purposes or truncate it. Don&#8217;t want a 5GB table sitting around that doesn&#8217;t help you more than the initial deadlock log.
  
To make this automated all we need is a SQL Job to run every 30 minutes or so. That&#8217;s what I have mine set at and it seems to be a good cycle. Deadlocks really should not come up that often and if they are then you probably know of the issue in advance and to be honest this article won&#8217;t do you a bit of good other than future reference. 

That&#8217;s it! Now you&#8217;re setup to know when deadlocks happen on your instances and can find your answers before they ask the questions.

Follow up links that I refer to often and will help along with this post
  
http://msdn.microsoft.com/en-us/library/ms188396.aspx
  
http://msdn.microsoft.com/en-us/library/ms178104(SQL.90).aspx
  
http://doc.ddart.net/mssql/sql70/1\_start\_8.htm