---
title: Disable SQL Agent Jobs with TSQL based on events
author: Ted Krueger (onpnt)
type: post
date: 2009-03-26T12:10:56+00:00
ID: 367
url: /index.php/datamgmt/datadesign/disable-sql-agent-jobs-with-tsql-based-o/
views:
  - 8591
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server Admin

---
Altering SQL Agent jobs during job runtime can give you some options as to how you can force other jobs
  
to react based on events. There are a listing of system procedures in the MSDB database available for
  
you to use so you can literally alter your entire timelines and schedules based on a failure, success or
  
even OS or network related issues. Here is a basic example of disabling and enabling a job
  
based on a monitoring task. 

In this example there is a requirement to monitor a table that is being used to monitor finish good allocation
  
to a manufacturing facility to prevent volume from going over capacity. 

Job 1 performs a monitoring task every 30 minutes. Basically querying the table that holds the count of the volume
  
the facility has received that day (starting at midnight). At midnight the volume is replenished by means of the value
  
specified in the same table. There is a need to monitor the capacity and once under 2000 units are remaining,
  
you are required to send notifications out to management. Notifications should go out once.
  
The key is the one notification. This is one option of getting around that using the system procedures to alter job properties. 

Step 1) create the table to monitor

sql
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[VOL_MONITOR](
	[VolumeID] [int] IDENTITY(1,1) NOT NULL,
	[Facility] [varchar](50) NOT NULL,
	[Volume] [int] NOT NULL,
	[Vol_Status] [int] NOT NULL,
 CONSTRAINT [PK_VOL_MONITOR] PRIMARY KEY CLUSTERED 
(
	[VolumeID] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, 
			IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, 
			ALLOW_PAGE_LOCKS  = ON, FILLFACTOR = 90) ON [PRIMARY]
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
```
Insert the data in order to test out monitor

sql
INSERT INTO DBO.[VOL_MONITOR]
VALUES ('AL Plant 1',20000,15000)
```
Step 2) create the job to monitor the volume count.

sql
DECLARE @jobId BINARY(16)
EXEC @ReturnCode =  msdb.dbo.sp_add_job @job_name=N'Capacity Restriction Monitor Plant 01', 
		@enabled=1, 
		@notify_level_eventlog=0, 
		@notify_level_email=0, 
		@notify_level_netsend=0, 
		@notify_level_page=0, 
		@delete_level=0, 
		@description=N'Monitors the levels of units allowe to be manufactured in plant 01.', 
		@category_name=N'[Uncategorized (Local)]', 
		@owner_login_name=N'{user}', @job_id = @jobId OUTPUT
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
/****** Object:  Step [caller]    Script Date: 03/26/2009 08:31:35 ******/
EXEC @ReturnCode = msdb.dbo.sp_add_jobstep @job_id=@jobId, @step_name=N'caller', 
		@step_id=1, 
		@cmdexec_success_code=0, 
		@on_success_action=1, 
		@on_success_step_id=0, 
		@on_fail_action=2, 
		@on_fail_step_id=0, 
		@retry_attempts=0, 
		@retry_interval=0, 
		@os_run_priority=0, @subsystem=N'TSQL', 
		@command=N'if (select vol_status from VOL_MONITOR) <= 2000
		  Begin
			EXEC msdb.dbo.sp_send_dbmail @recipients=''manager_group@companym.com'',
				@subject = ''Plant 01 volume reaching maximum capacity'',
				@body = ''Plant 01 volume reaching maximum capacity'',
				@body_format = ''HTML'',
				@profile_name = ''DBA01''
		  End', 
		@database_name=N'DBA05', 
		@flags=0
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
EXEC @ReturnCode = msdb.dbo.sp_update_job @job_id = @jobId, @start_step_id = 1
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
EXEC @ReturnCode = msdb.dbo.sp_add_jobschedule @job_id=@jobId, @name=N'30 minutes', 
		@enabled=1, 
		@freq_type=4, 
		@freq_interval=1, 
		@freq_subday_type=4, 
		@freq_subday_interval=30, 
		@freq_relative_interval=0, 
		@freq_recurrence_factor=0, 
		@active_start_date=20090324, 
		@active_end_date=99991231, 
		@active_start_time=0, 
		@active_end_time=235959
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
EXEC @ReturnCode = msdb.dbo.sp_add_jobserver @job_id = @jobId, @server_name = N'(local)'
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
COMMIT TRANSACTION
GOTO EndSave
QuitWithRollback:
    IF (@@TRANCOUNT > 0) ROLLBACK TRANSACTION
EndSave:
```
So far we have successfully created a table to hold our capacity values that we negate based on units
  
sent to eh facility and we have a job to monitor the capacity. Problem is the job will send out an
  
email to management every 30 minutes which is not something managers like much. In order to keep the
  
DBA from manually going in after the notification is sent we need to disable the job. We can
  
use sp\_udpate\_job to get that task done.

First query the sysjob table in MSDB to grab the job_id. You can also use the name of the job but I
  
think that is prone to typos and copying the job_id directly out of sysjob is a bit more stable.

sql
select job_id,[name] from msdb.dbo.sysjobs
```
This shows &#8220;8386ED61-E2A8-4DE5-B54C-4A7DFD3CDDEC&#8221; for the Capacity Restriction Monitor Plant 01 job_id

Syntax for sp\_update\_job is as follows. One key note is you can use job\_name or job\_id to update the job with
  
this system procedure BUT you cannot specify both. Other questions I hear are does the SP update other properties
  
when you do not specify them. The answer is no, the SP will only update what parameter you specify. 

syntax

sql
sp_update_job [ @job_id =] job_id | [@job_name =] 'job_name'
     [, [@new_name =] 'new_name' ] 
     [, [@enabled =] enabled ]
     [, [@description =] 'description' ] 
     [, [@start_step_id =] step_id ]
     [, [@category_name =] 'category' ] 
     [, [@owner_login_name =] 'login' ]
     [, [@notify_level_eventlog =] eventlog_level ]
     [, [@notify_level_email =] email_level ]
     [, [@notify_level_netsend =] netsend_level ]
     [, [@notify_level_page =] page_level ]
     [, [@notify_email_operator_name =] 'email_name' ]
          [, [@notify_netsend_operator_name =] 'netsend_operator' ]
          [, [@notify_page_operator_name =] 'page_operator' ]
     [, [@delete_level =] delete_level ] 
     [, [@automatic_post =] automatic_post ]
```
So to form this call and disable the job we need to add to the job this statement.

After the db mail send put issue a Go and then add

sql
Exec msdb.dbo.sp_update_job @job_id = '8386ED61-E2A8-4DE5-B54C-4A7DFD3CDDEC' , @enabled = 0
```
Now when the job runs it will send the notifications out and disable the job so no further
  
notifications will be sent. 

Every night at midnight we stated the volume should be replenished to the maximum allowed units. This is
  
simply another job to reset the Vol_Status in our table to the value in Volume. The added requirement we need to
  
add to that job is re-enabling out monitor. If we don&#8217;t then the call to change the enable value in sysjob will remain
  
0 and the we break out monitoring process. So create another job scheduled for midnight with the statement of

sql
Update mem_vol set vol_status = volume
Go
Exec msdb..sp_update_job @job_id = '8386ED61-E2A8-4DE5-B54C-4A7DFD3CDDEC' , @enabled = 1
Go
```
Now the process has gone through a complete cycle and the monitor and volume count starts clean.