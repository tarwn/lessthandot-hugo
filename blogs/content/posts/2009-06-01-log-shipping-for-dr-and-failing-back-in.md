---
title: 'Log Shipping for DR and failing back in case of disaster.  The cheap way!'
author: Ted Krueger (onpnt)
type: post
date: 2009-06-01T13:49:12+00:00
ID: 452
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/log-shipping-for-dr-and-failing-back-in/
views:
  - 8983
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
Often I hear the argument on log shipping for disaster recovery not being a good choice due to the difficulty in failing back to the primary site. I never really got this argument and enjoy it when people try to bring it up when trying to sell expensive third party tools to replicate off-site. Of course if you have the money, there are far better choices to get your data to the DR site. If you don't like most groups, then this is an easy and stable way to get your failback scenario setup.

Basically the reason it is hard to fail back to the primary site is due to the data not being in sync. Just saying that out loud promotes a few options to easily fall back. The easiest solution is the do the exact same thing you are doing on the primary site while waiting for a disaster. Backups! This is a sort of poor man’s strategy to get your primary site back in sync from a failover.

Given the task of first deciding what your DR strategy is based on the business allowance of loss and systems required, this can be accomplished with a few small tasks on the DR instances. I added the business objective in here because this should always drive your DR strategy. The data loss allowance, time of fail over, systems required for operations and on, will all determine what technology you utilize. Given that and if log shipping is accepted by the strategy, you can do most of the hard work with the SQL Server Agent and using the already composed backup plans.

The basic steps here are to first monitor the status of the database on a particular interval. In this example I do this every minute in a different job. Note: watch your msdb job history retention. You can grow pretty fast when running jobs in short intervals. So if a database that is in standby is found to be online either by automated tasks or manual tasks, the database was brought online due to a disaster event. With the database online you can then script your entire backup strategy on the DR instances to start backing up the data the company is now relying on to survive. Let’s take a look at a script that will start you off in that direction

In the below examples I'm using a database named DBA05 to hold my primary table that consists of the monitored databases and the scripts to execute to start backups rolling.

sql
IF OBJECT_ID ('DBA05.dbo.BACKUP_PLANS','U') IS NOT NULL
	BEGIN
		DROP TABLE DBA05.dbo.BACKUP_PLANS
	END

CREATE TABLE BACKUP_PLANS
	(
		IDENT INT IDENTITY(1,1) PRIMARY KEY,
		DBNAME SYSNAME,
		JOB_NAME SYSNAME,
		AGENT_SCRIPT VARCHAR(max),
		AGENT_STATUS	BIT
	)

INSERT INTO BACKUP_PLANS
VALUES (
'DBA05',
'DBA05 DR Full Backp Set',
'USE [msdb];
BEGIN TRANSACTION
DECLARE @ReturnCode INT
SELECT @ReturnCode = 0

IF NOT EXISTS (SELECT name FROM msdb.dbo.syscategories WHERE name=N''Database Maintenance'' AND category_class=1)
BEGIN
EXEC @ReturnCode = msdb.dbo.sp_add_category @class=N''JOB'', @type=N''LOCAL'', @name=N''Database Maintenance''
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback

END

DECLARE @jobId BINARY(16)
EXEC @ReturnCode =  msdb.dbo.sp_add_job @job_name=N''DBA05 DR Full Backp Set'', 
		@enabled=1, 
		@notify_level_eventlog=0, 
		@notify_level_email=0, 
		@notify_level_netsend=0, 
		@notify_level_page=0, 
		@delete_level=0, 
		@description=N''This job was scripted automatically to start ful lbackups on the database DBA05.'', 
		@category_name=N''Database Maintenance'', 
		@owner_login_name=N''someonewithrights'', @job_id = @jobId OUTPUT
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
EXEC @ReturnCode = msdb.dbo.sp_add_jobstep @job_id=@jobId, @step_name=N''Execute Full Backup Command'', 
		@step_id=1, 
		@cmdexec_success_code=0, 
		@on_success_action=1, 
		@on_success_step_id=0, 
		@on_fail_action=2, 
		@on_fail_step_id=0, 
		@retry_attempts=0, 
		@retry_interval=0, 
		@os_run_priority=0, @subsystem=N''TSQL'', 
		@command=N''DECLARE @BACKUPSET VARCHAR(200)
				SET @BACKUPSET = ''''C:SQLBACKUPSDBA05DBA_FULL_'''' +	
				REPLACE(REPLACE(REPLACE(CONVERT(VARCHAR(19),GETDATE(),121),''''-'''',''''''''),'''':'''',''''''''),'''' '''','''''''') + ''''.BAK''''
				BACKUP DATABASE DBA05
				TO DISK = @BACKUPSET'', 
		@database_name=N''tempdb'', 
		@flags=4
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
EXEC @ReturnCode = msdb.dbo.sp_update_job @job_id = @jobId, @start_step_id = 1
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
EXEC @ReturnCode = msdb.dbo.sp_add_jobschedule @job_id=@jobId, @name=N''Hourly Full Backup'', 
		@enabled=1, 
		@freq_type=8, 
		@freq_interval=1, 
		@freq_subday_type=8, 
		@freq_subday_interval=1, 
		@freq_relative_interval=0, 
		@freq_recurrence_factor=1, 
		@active_start_date=20080122, 
		@active_end_date=99991231, 
		@active_start_time=23000, 
		@active_end_time=235959
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
EXEC @ReturnCode = msdb.dbo.sp_add_jobserver @job_id = @jobId, @server_name = N''(local)''
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback
COMMIT TRANSACTION
GOTO EndSave
QuitWithRollback:
    IF (@@TRANCOUNT > 0) ROLLBACK TRANSACTION
EndSave:',
0)
```
As you can see the table is pretty simple. We need to know the database to monitor, the job creation script and the status of the job to keep us from recreating a job already set in place. This is also nice if you have one DR site and only fail over a few systems to it. Executing this gives us the ability to dynamically monitor only certain databases and create the jobs on the fly. It would also be possible to create the jobs ahead of time and simply enable them in the event. I like the first method because it seems to give me one script that you can potentially use across many instances with little change if you get more creative with the job creation script. 

Now that you have the table setup, you can get going on the monitor job. The code below will first use the sys.databases system view to validate the status of the databases. This joined to our table of which databases to monitor will filter out any other databases on the instance. Once the databases that are online and a flag for agent status of 0, the job will proceed to setup the SQL Agent jobs for each given script found in the table. After all is done successfully the status is updated.

I wrote this for this blog and tested it out so the script works as is but you will need to alter it greatly in order to fit it into your environment. It was also written in 2005 as you can see with the TRY…CATCH. 

sql
BEGIN TRY
BEGIN TRANSACTION

If Exists(SELECT 1
				FROM sys.databases sys
				INNER JOIN BACKUP_PLANS plans On sys.[name] = plans.DBNAME
				where state_desc = 'ONLINE')
 Begin
		Select 
		ROW_NUMBER() OVER(Order By [Name]) as ROWID
		,[Name]
		,AGENT_SCRIPT
		,JOB_NAME
		Into #dbs 
		FROM sys.databases sys
		INNER JOIN BACKUP_PLANS plans On sys.[name] = plans.DBNAME
		where state_desc = 'ONLINE' AND AGENT_STATUS = 0
	
	DECLARE @loop INT
	DECLARE @cmd varchar(max)
	DECLARE @jobname sysname

	SET @loop = 1

	WHILE @loop <= (Select Count(*) From #dbs)
		BEGIN
			Select 
				@cmd = AGENT_SCRIPT,
				@jobname = JOB_NAME
			From #dbs
			Where ROWID = @loop
	
			If Exists(Select 1 
							From msdb.dbo.sysjobs
							Where [Name] = @jobname
							And (Select agent_status from backup_plans where job_name = @jobname) = 0)
				Begin
					Exec msdb.dbo.sp_delete_job @job_name = @jobname
				End

			Exec(@cmd)		

			UPDATE BACKUP_PLANS 
				SET AGENT_STATUS = 1
				WHERE JOB_NAME = @jobname

			SET @loop = @loop + 1
		END
		Drop Table #dbs
 End

   COMMIT
END TRY
BEGIN CATCH
  IF @@TRANCOUNT > 0
     ROLLBACK

  DECLARE @ErrMsg nvarchar(4000), @ErrSeverity int
  SELECT @ErrMsg = ERROR_MESSAGE(),
         @ErrSeverity = ERROR_SEVERITY()

  RAISERROR(@ErrMsg, @ErrSeverity, 1)
END CATCH
```
To fully get everything up and running you should add differentials and more importantly, log backups into this table. Given your backup strategy and log backup plans on the primary site, you should already know the acceptance of the business for point in time recovery. Follow that same strategy! Once this is all done you can then successfully monitor the DR site for a failover and automatically start backups so you have a point in time to backup the tail logs and get your data in sync quickly. Or as quickly as the size of the pipes to the offsite are. This will also save you in case of a hardware failure on the DR site. Yes, don’t forget to backup the DR servers as well to tape and or other means of recovery. You don’t want to fail over and then have the DR site fail in a day. Not acceptable! Remember that the primary site, once brought back up and ready for failback, will need the databases in recovering status to apply the logs or diff's to it. If you recover the site completely and prior to a scheduled full backup, you can restore the last full backup and all other backup to get to the time you had the disaster in recovering mode. Then copy the minimal required backups that started on the DR site over. 

I’ve used this method in a few facilities and it works well when budget is small on DR. That is as most of us know the case in most environments. If the databases are extremely large it may not be the best copying the backups back, however in most cases with databases that size, budget is typically more accessible.