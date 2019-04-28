---
title: Do automated restore tests on your SQL-Backups.
author: Christiaan Baes (chrissie1)
type: post
date: 2008-05-17T05:53:56+00:00
ID: 26
url: /index.php/datamgmt/dbprogramming/mssqlserver/do-automated-restore-tests-on-your-sql-b/
views:
  - 2581
categories:
  - Microsoft SQL Server

---
I found out yesterday that my SQL server backups were corrupt and so I couldn&#8217;t restore them (happy me). So I went out on Google and combined a few things to make this

```tsql
declare @verifystatement nvarchar(250)
declare @backupdevice nvarchar(250)
declare @err int

set @backupdevice =     (SELECT     TOP 1 [physical_device_name]
            FROM     msdb.dbo.backupmediafamily backupmediafamily
                JOIN msdb.dbo.backupset backupset ON backupmediafamily.media_set_id = backupset.media_set_id
                and backupset.backup_start_date = (    SELECT max(backup_start_date)
                                    FROM msdb.dbo.backupset child
                                    WHERE child.database_name = backupset.database_name and child.type = 'D')
                and database_name = 'TexDatabase'
                and backupset.type = 'D')

set @verifystatement = 'restore verifyonly from disk = ''' + @backupdevice + ''''
exec sp_executesql @verifystatement
SELECT @err = @@error

if @err &lt;&gt; 0
        begin
            insert into utils.dbo.tbl_backupverify(BackupDevice,BackupStatus,BackupError, VerifyStatement)
            values (@backupdevice, 'Failed', @err, @verifystatement)
        end
        else
        begin
            insert into utils.dbo.tbl_backupverify(BackupDevice,BackupStatus)
            values (@backupdevice, 'Succes')
        end```
Just put it in a Store procedure and run it as a job.

This is the tbl_BackupVerify create table statement.

```
if exists (select * from dbo.sysobjects where id = object_id(N'[dbo].[tbl_BackupVerify]') and OBJECTPROPERTY(id, N'IsUserTable') = 1)
drop table [dbo].[tbl_BackupVerify]
GO```
```
CREATE TABLE [dbo].[tbl_BackupVerify] (
    [Id] [int] IDENTITY (1, 1) NOT NULL ,
    [BackupDevice] [nvarchar] (250) COLLATE Latin1_General_CI_AS NOT NULL ,
    [BackupStatus] [nvarchar] (50) COLLATE Latin1_General_CI_AS NOT NULL ,
    [VerifyDate] [datetime] NOT NULL ,
    [BackupError] [bigint] NULL ,
    [VerifyStatement] [nvarchar] (500) COLLATE Latin1_General_CI_AS NULL 
) ON [PRIMARY]
GO
```
You could add this as a scheduled job in sql-server.

Just don&#8217;t forget to check the log table or make it send an email.