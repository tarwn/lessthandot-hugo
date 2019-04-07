---
title: SQL Agent Job Notifications
author: Ted Krueger (onpnt)
type: post
date: 2008-12-01T12:41:40+00:00
url: /index.php/datamgmt/datadesign/sql-agent-job-notifications/
views:
  - 12298
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server Admin

---
This falls under DBA scripting and automation. I am going to try to key on these things in the next few weeks after reading Dan&#8217;s blog titled, &#8220;Scripting DBA Actions&#8221; here http://blogs.msdn.com/dtjones/archive/2008/11/23/scripting-dba-actions.aspx

If you have a landscape that is of some size then on most of your instances you&#8217;re going to find yourself mixed into dozens of SQL Agents Jobs that can become a daily task to ensure they ran correctly. Again this is on the basis you have no monitoring tools that will already notify you of problems and failures. Now we all know there is the Notifications option on all jobs. This is great for basic failures, successful runs and completions but if you&#8217;ve ever had to write a job that really did something other than a backup task you&#8217;re going to find the need for more options in notifying you when certain things fail and or complete. This is also helpful when you don&#8217;t want large SQL Tasks to stop an entire job but in the same event let you know one update may have not been successful.

So let&#8217;s say we have a job that first downloads from an internet location, backs up a table, bulk imports the new data from the download, exports to excel worksheets for sales exception reporting and then updates several key points in sales data. 

That&#8217;s a heavy task and if we have a failure on per say in the excel export from a SQL Task that just selects some data, do we really want to forego the update? No, not really sense the update is key to the business running. 

So SQL Server 2005 and up implemented a long awaited method of emailing without the need for one of outlook to be installed and or xp_ to use cdonts which opened our instances to security risks. To be honest there were dozens of points on SQL Server 2005 that made the hair on my neck stand up with excitement and this was on the top of the list.

So let&#8217;s first setup you instance for database mail abilities. First you&#8217;ll have to verify that database mail is enabled. you can do that with surface area configuration by going into the surface area configuration features. Then select Database Mail and make sure it is Enabled. Remember by default most of SQL Server 2005 features are disabled. You can also run

<pre>USE Master
GO
sp_configure 'show advanced options', 1
GO
reconfigure with override
GO
sp_configure 'Database Mail XPs', 1
GO
reconfigure 
GO
sp_configure 'show advanced options', 0
GO</pre>

Now you&#8217;re going to need a profile and account. Out of scope for me to go step by step through this process and there are hundreds of articles out there on how to do it. Probably hundreds on doing what I&#8217;m talking about also but what&#8217;s the internet if it isn&#8217;t full of repeated articles stating the same thing 😉 One thing I do like to do is create a profile named &#8220;DBA {instance name}&#8221; and an account to match that naming. This way I know where it comes from and which aspect of the instance is sending it without even opening the mail. At least it gets me to open it quicker than most that is.

So now that you have that all running you can test it by this simple little script

<pre>EXEC msdb.dbo.sp_send_dbmail @recipients='you@yourcompany.com',
@subject = 'My Test Email',
@body = 'It works!',
@profile_name = 'SQL DBA'</pre>

You should have received the email. I you didn&#8217;t you can either wait for me to write a troubleshooting database mail blog or following yourself to http://msdn.microsoft.com/en-us/library/ms188663.aspx

Typically if it doesn&#8217;t work you&#8217;re blocking it on the server level or at the mail server level. Good thing to check is port blocking and or your companies chosen firewall/antivirus software.

OK this is cool! I can email all kinds of things. Actually you would be very surprised. You can send attachments on the fly, query results, HTML formatting, excel formatting and the list goes on. These all reasons I was excited to see the advent of database mail.

I would take some time and read on the sp\_send\_dbmail proc http://msdn.microsoft.com/en-us/library/ms190307.aspx

So for a simple job notification and example of what you can do we can create something that has one main process, a success notification and a failure notification. 

First create a new job. Then add a step. Something like this

<pre>USE [msdb]
GO
BEGIN TRANSACTION
DECLARE @ReturnCode INT
SELECT @ReturnCode = 0

IF NOT EXISTS (SELECT name FROM msdb.dbo.syscategories WHERE name=N'[Uncategorized (Local)]' AND category_class=1)
BEGIN
EXEC @ReturnCode = msdb.dbo.sp_add_category @class=N'JOB', @type=N'LOCAL', @name=N'[Uncategorized (Local)]'
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback

END

DECLARE @jobId BINARY(16)
EXEC @ReturnCode = msdb.dbo.sp_add_job @job_name=N'Test Notifications', 
  @enabled=1, 
  @notify_level_eventlog=0, 
  @notify_level_email=0, 
  @notify_level_netsend=0, 
  @notify_level_page=0, 
  @delete_level=0, 
  @description=N'Delete this sometime soon to clean up', 
  @category_name=N'[Uncategorized (Local)]', 
  @owner_login_name=N'{owner}', @job_id = @jobId OUTPUT
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback

EXEC @ReturnCode = msdb.dbo.sp_add_jobstep @job_id=@jobId, @step_name=N'Caller Task', 
  @step_id=1, 
  @cmdexec_success_code=0, 
  @on_success_action=4, 
  @on_success_step_id=2, 
  @on_fail_action=4, 
  @on_fail_step_id=3, 
  @retry_attempts=0, 
  @retry_interval=0, 
  @os_run_priority=0, @subsystem=N'TSQL', 
  @command=N'Select 1', 
  @database_name=N'DBA', 
  @flags=0
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback

EXEC @ReturnCode = msdb.dbo.sp_add_jobstep @job_id=@jobId, @step_name=N'Success Caller', 
  @step_id=2, 
  @cmdexec_success_code=0, 
  @on_success_action=1, 
  @on_success_step_id=0, 
  @on_fail_action=4, 
  @on_fail_step_id=3, 
  @retry_attempts=0, 
  @retry_interval=0, 
  @os_run_priority=0, @subsystem=N'TSQL', 
  @command=N'Declare @sub varchar(1000)
Declare @bd varchar(2000)

Set @sub = ''Success on SQL Agent '' + @@servername
Set @bd = ''Success on SQL Agent '' + @@servername + '' for job task caller task at + getdate() + '' requiring no attention so go back to sleep'' 

EXEC msdb.dbo.sp_send_dbmail @recipients=''you@yourcompany.com'',
@subject = @sub,
@body = @bd,
@profile_name = ''SQL DBA''
', 
  @database_name=N'msdb', 
  @flags=0
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback

EXEC @ReturnCode = msdb.dbo.sp_add_jobstep @job_id=@jobId, @step_name=N'Failure Caller', 
  @step_id=3, 
  @cmdexec_success_code=0, 
  @on_success_action=2, 
  @on_success_step_id=0, 
  @on_fail_action=2, 
  @on_fail_step_id=0, 
  @retry_attempts=0, 
  @retry_interval=0, 
  @os_run_priority=0, @subsystem=N'TSQL', 
  @command=N'Declare @sub varchar(1000)
Declare @bd varchar(2000)

Set @sub = ''Failure on SQL Agent '' + @@servername
Set @bd = ''Failure on SQL Agent '' + @@servername + '' occurred at + getdate() + '' requiring immediate attention'' 

EXEC msdb.dbo.sp_send_dbmail @recipients=''you@yourcompany.com'',
@subject = @sub,
@body = @bd,
@profile_name = ''SQL DBA''', 
  @database_name=N'msdb', 
  @flags=0
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
EXEC @ReturnCode = msdb.dbo.sp_update_job @job_id = @jobId, @start_step_id = 1
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
EXEC @ReturnCode = msdb.dbo.sp_add_jobserver @job_id = @jobId, @server_name = N'(local)'
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
COMMIT TRANSACTION
GOTO EndSave
QuitWithRollback:
IF (@@TRANCOUNT > 0) ROLLBACK TRANSACTION
EndSave:</pre>

You can test this job to see how it works by running as is and also changing the mail &#8220;Caller Task&#8221; so something like select 1/0.

Obviously I like the right click script option. The important things you need to know here is the workflow sense I&#8217;m sure if you&#8217;re reading this you know how to create a SQL Job already and don&#8217;t need me to tell you how.

The main task will require you to push to the successful notification upon a successful run and then the failure on that event. So in SSMS you&#8217;re going to have it look like this

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//job1.gif" alt="" title="" width="703" height="349" />
</div>

Make sure you use your successful notification task in the On success action or you&#8217;re job will have an unreachable task and complain when you save it. Then make sure your Success Caller reports success but if failure points to the failure caller task. In your failure task your always going to want to report failure on success. So you&#8217;re other tasks advanced options will appear as 

Success&#8230;

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//job2.gif" alt="" title="" width="702" height="246" />
</div>

Failure&#8230;

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//job3.gif" alt="" title="" width="705" height="323" />
</div>

I also recommend notification services and setting up the normal failure notifications in the SQL job. This way if all fails you should get it queued up.

So now you can email to your heart&#8217;s content. Well until you show up on the black list anyhow 😉