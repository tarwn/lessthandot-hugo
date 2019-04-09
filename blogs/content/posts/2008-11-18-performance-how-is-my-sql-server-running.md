---
title: Performance (How is my SQL Server running?)
author: Ted Krueger (onpnt)
type: post
date: 2008-11-18T16:00:10+00:00
ID: 208
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/performance-how-is-my-sql-server-running/
views:
  - 7373
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
In a previous entry I talked about how you should pressure management to purchase SQL Server monitoring tools. This in my eyes is important for more than just the fact you can see quickly when you have problems on your instances but also because we as any associate working for the company that was kind enough to employ us is a business entity. That means we need the business to run with minimal costs associated with it. What that boils down to with having something like Idera SQLdm or Quests monitoring and the lot is we as DBAs spend a fraction of the time troubleshooting and putting in preventative structures to our database designs. 

So now reality sets in. I know there are companies out there that one either do not have the funds to spend the cost that comes with monitors tools or two they just say no to them because they aren't all the smart and sense a DBA is salary in most cases the DBA spending 20 hours getting indexes in vs. an hour makes no difference. Does it? Yes it does business majors. If I spend 20 hours figuring out my bottle necks and indexing to prevent them vs. spending an hour on that task then I just missed out on 19 hours I could have spent working on the next project. It really is a simple equation and in the long run the business always loses out when they do not provide the tools to make our jobs more productive. Where was I? Oh yea, reality! 

Something I'm commonly surprised is not installed on every SQL Server landscape is a primary SSRS database server. Don't get me going on that cause we'll be here all day. On that primary SSRS instance there should be a SQL Server DBA folder and in there all your favorite commonly run reports but most of all the SQL Server 2005 Dashboard. What's that? This is where I love to talk to people that think Microsoft does nothing to make our lives easy and they hate us all. Sorry but they do a pretty damn good job along with make our lives easy. Well, if you can follow simple directions 😉

The SQL Server 2005 Performance Dashboard Reports is something that has been out there sense SP2. Basically all these reports do is expose the dynamic management objects we have now with 2005+ versions but they put it in a package in SSRS that makes it very easy for us to see historic data and real-time views of our performance. Some of the nicest features are the missing index views, expensive query reports, IO and Wait statistics and of course the CPU statistics. Now I know this can all be had by executing the DMV/DMF's in SSMS but why not show me a graph? I like graphs. Easy to tell when I have a big bar surrounded around little ones and wow, I can click it and see the SPID that did it and the showplan of the query that was killing it. No that doesn't mean your lazy if you like the same things I do. It means your efficient and when you're in a Sr. level position you know this is more important than knowing how to weed out lock statistics from sp_lock and narrow it back to some sessions.

So let's install the dashboard the way I did it and also point out what I needed to do in order to make it work. There are a few catches in it.

First go here and download the msi to install the rdl's 

http://www.microsoft.com/downloads/details.aspx?FamilyId=1d3a4a0d-7e0c-4730-8204-e419218c1efc&displaylang=en

I'm assuming you at least have SSRS installed on your PC but preferably you've talked your management into a designated SSRS server by now.

When you run the msi all it does is decompress some rdl's and a .sql script to the typical folder location of “C:Program FilesMicrosoft SQL Server90ToolsPerformanceDashboard”

First thing is you need to put the procedures in the msdb on the instance you want to monitor. Microsoft has the scrip tin the folder you just added name, “setup.sql”. creative huh? Open ths script on the instance and run it. Don't worry because if you are a smart DBA you have a backup of the sys databases ;). Really though all this does is install some MS_PerfDashboard… procedures to the msdb. Yes make your sys backups if you haven't first though. 

Now open up VS.NET or BIDS and create a new Report Server Project named “SQL Server DBA”. In the fresh project right click the Reports folder and go to Add, Existing Item.

In the dialog browse to the location the rdl files are and select them all and click Add. Now right click the Shared Data Sources folder and click Add New Data Source. leave DataSource1 name as is unless you want to change all the datasets in the rdl files. Personally I do that because I control my data sources critically but it's up to you. My job is to get it running for you and then you make your own choices. So now click Edit and enter your server name, leave windows authentication and the then any db.

Now double click the “performace\_dashbaord\_main.rdl” and hit preview. You were probably shown either permissions errors or a nasty datatime overflow error like this, “Difference of two datetime columns caused overflow at runtime”. First, permissions you can handle. You need server view state and access to sys views. The overflow is a bit different. There is an easy fix for it. Basically it's from sessions over 24 hours in time. To fix this go into the msdb and modify the procedure MS\_PerfDashboard.usp\_Main_GetSessionInfo to this

sql
USE [msdb]
GO
ALTER procedure [MS_PerfDashboard].[usp_Main_GetSessionInfo]
as
begin
 select count(*) as num_sessions,
  sum(convert(bigint, s.total_elapsed_time)) as total_elapsed_time,
  sum(convert(bigint, s.cpu_time)) as cpu_time, 
  sum(convert(bigint, s.total_elapsed_time)) - sum(convert(bigint, s.cpu_time)) as wait_time,
  sum(convert(bigint, CAST ( DATEDIFF ( minute, login_time, getdate()) AS BIGINT)*60000 + DATEDIFF ( millisecond, DATEADD ( minute,
    DATEDIFF ( minute, login_time, getdate() ), login_time ),getdate() ))) - sum(convert(bigint, s.total_elapsed_time)) as idle_connection_time,
  case when sum(s.logical_reads) > 0 then (sum(s.logical_reads) - isnull(sum(s.reads), 0)) / convert(float, sum(s.logical_reads))
   else NULL
   end as cache_hit_ratio
 from sys.dm_exec_sessions s
 where s.is_user_process = 0x1
end
```
Important to note this is not my fix but from here

http://blogs.msdn.com/sqlrem/archive/2007/03/07/Performance-Dashboard-Reports-Now-Available.aspx

Give credit where credit is due!

Now go back to the report and refresh it if you already did not see the report generated.
  
You should see something like this for the overview 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ss_dashboard.gif" alt="" title="" width="989" height="590" />
</div>

Nice isn't it? This is about as far as I'm going to go with it sense the links in the reports are pretty self explaining and when you click and export the statistics to formats and start on your performance increasing quest you'll learn more of this Microsoft provided monitoring tool than I. I just wanted to get you off your feet and flying. Hope I did just that with saving a little money.