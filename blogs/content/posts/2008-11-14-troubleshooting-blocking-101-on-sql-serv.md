---
title: Troubleshooting Blocking 101 on SQL Server
author: Ted Krueger (onpnt)
type: post
date: 2008-11-14T19:05:33+00:00
ID: 206
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/troubleshooting-blocking-101-on-sql-serv/
views:
  - 13828
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
Today started off around 12:03 AM to my treo chimming about every 5 minutes. It wasn't the way I wanted to wake up but we probably all know the feeling at one point or another. So why was my treo talking so much? Well at 12:03 AM a transaction decided to start blocking some updates. At this point SQLdm decided to let me know. By the time I logged in to check the blocked transactions were in limbo for around 5203 seconds. Put yourself in the users shoes and sit there for 5203 second sto wait for your transaction to finish 😉 Right, you're not going to be happy. Needless to say this followed with the oncall person calling me. 

Hence started the troubleshooting steps which this is going to go over. Blocking to me and in my experience is one of the most common performance issues after you get beyond the hardware setup and database setup itself. Blocking often is out of your control all together. Why I say that is because 9 time out of 10 blocking comes from a poorly designed application or small sector to an application. I don't mean poorly in the entire application is bad and uninstall it but more so to the fact that you're going to need to handle the downfalls, find out how you are missusing the application or get on the phone demanding a fix. In this case the application had been running for some time though and the blocking came on quickly and got bad real fast. This is common and don't think your protected from it because it will happen at some point in your career working on database servers.

So the first step is to buy some third party tool to monitor these things. Huh? But we have no money for that. You should find it! A tool like Idera's diagnostic manager can save you hundreds of hours and save you from a problem becoming so bad it's hard to bring it back and at the same time the users hate you. 

Alright so you won't listen and fight for the PO. Here is the cheapest and less straining way on your SQL Server to send notifications if blocking is happening.

First I recemmend looking at setting the threshold. You can read more on that and blocking from Rob's blog blogs.technet.com/rob/archive/2008/05/26/detecting-sql-server-2005-blocking.aspx

For now let's be old fassion and cheap...

First query the master.dbo.sysprocesses view. There is a column named "blocked". If the value is 1 then you have blocking going on. So if you wanted to do something really simple you could create a job that runs to check for this and send an email out to you.

```sql
Declare @body varchar(1000)
Set @body = 'Blocking on ' + @@servername 

if Exists(select 1 from master.dbo.sysprocesses where blocked = 1)
 EXEC msdb.dbo.sp_send_dbmail @recipients='you@yourcompany.com;myphone@provider.com',
  @subject = 'Blocking notification',
  @body = @body,
  @profile_name = 'SQL DBA'
```
Yes that is crude. Not real great at all. I just wanted to show you that you can do it and there are mcuh better ways but not the point of this article 🙂

So what I had to do next now that I knew blocking was occuring was find out the blocking transaction. Again using SQLDM this is easy but sp_lock will give you the same information.

```sql
Exec sp_lock
```
What I could see then was the transaction locking the object and another transaction trying to grab another lock on it. My write up on deadlock notifications is close to come for how this could go even farther south.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//lock_results.gif" alt="" title="" width="628" height="348" />
</div>

Notice the intent shared lock and then 165 trying to issue an intent exclusive lock. That doesn't work so well. We have our blocking situation and knwo our ID's associated. This was all I needed to see who was doing it. Now we have to find out why and how to fix it. Usually you don't have alot of time if this come on all the sudden either.

So the next step is to get the batch 177 is sending over. The way to do this is first using the dm\_exec\_requests DMV to grab the sql\_handle and then check the dm\_exec\_sql\_text function to return the text. In this example I'm going to use my own session id to return the text. Obviously I fixed my blocking issues so I can't do it on that or I might not have a job rate now 😉

so first my ID is 208

```sql
select * from sys.dm_exec_requests where session_id = 208
```
This returned a sql_handle of 0x020000005B7A3A210C1B9AEEC5467BB6AFC4BA74F1C4964A for my session id

now we can grab the text

```sql
select * from  sys.dm_exec_sql_text(0x02000000E7CB3C0ADF13F985EC06EB70C8FD4EB6F9F686BA) 
```
which returned "select * from sys.dm\_exec\_requests"

We could also use a CROSS APPLY to the sys.dm_exec-connections to grab all the SQL text like

```sql
select 
 *
from 
sys.dm_exec_connections a
CROSS APPLY sys.dm_exec_sql_text(most_recent_sql_handle) 
```
Nice information to have for your analysis

Well in this mornings case it returned a query a bit larger but you see how I got there in this case. Once I had the query I was able to make sure SQL Server was first using indexes correctly. Yes people you should always check your execution plan. No matter how small the query is!!! In this case I did add and index increasing the performance by about 40%. Almost fixed it. So I thought anyhow. Then I checked statistics and there wasn't much there. Second was to update the statistics and also check fragementation on the other supporting indexes. Still the blocking was coming up. 

Next was to check wait times. What I saw was there was a ASYNC\_NETWORK\_IO wait. Everyone loves blamming the network but in this case I wasn't convinced because I checked the statistics of the network and the teaming on the servers and all was well. Although if you see these long waits you should ask your friendly network admin to check his or her statistics or if you have the ability go after it. Mostly if the blocking comes up all the sudden like this one did. If a table grows for some rason beyond the means of the systems written ability to handle it there is a good chance it is trying to pull so much data over the wire that it will bring you back to a bloated table and a cure.

I was left to look at both the blocking statement and what was being blocked. This showed me that the IS lock which returned around 3000 rows of data was open but something else was sending UPDATE's with the IX lock to the same object. I was narrowing it down now mostly to a poor process in the system. Here is my problem at this point. The system uses one sql server authenticated account to connect to the database. Argh!!! If you are a developer reading this please do not do this. It makes the DBA's life hard. I have no idea which part of the system is doing this. Only that the system was doing it. So now is the time to send an email to the company. So I did and they replied quickly thankfully with the exact service that was issuing the SELECT. This brought me to a windows service which I turned off and the blocks were gone. Great, but the business needs that thing huh? 🙁

Long story short I found there were status keys not being set right and I created a sql job to set them to prevent the program that was processing row for row and sending updates on each row to the same tables hence causing the blocking situation.

So you see the steps now...

  * identifiy the locking IDs
  * identify the statements and optimize. This 90% of the time will fixs it
  * identify the system or part of the system that is causing the problem
  * turn it off if you can while you work with the software company or get a workaround in while your hands are tied.
  * go back to sleep