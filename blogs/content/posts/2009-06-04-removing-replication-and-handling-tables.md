---
title: Removing replication and handling tables with identity seeds
author: Ted Krueger (onpnt)
type: post
date: 2009-06-04T11:42:51+00:00
ID: 455
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/removing-replication-and-handling-tables/
views:
  - 7773
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
If you ever replicate tables that have identity seeds on columns you're more than likely going to have a headache on your first try of setting this up. There are several things you have to consider and take into account when replicating these types of columns. Of course you want them replicated and you can very easily do so while retaining the integrity between the tables. However, if you ever remove replication from the subscriber table, you're going to find yourself in a sticky situation no matter what setting you have for the replication. 

There are a few catches to mention. The first is not to use the drop option in the @pre\_creation\_cmd parameter of the sp_addarticle execution if you want to retain the last set seed on the subscriber table. I say this because if you go on your subscriber after initializing it and check your seed, it will be set to NULL. This is normal for how the drop and even the truncate option handles the seed. You'll see the same results if you use truncate on a table that has a identity column. What you'll want to do is use the delete in this option to retain the seed where it is at the subscriber level.
  
The second catch is identity_insert issues. If you change the creation command and then initilize your subscriber, you will soon find that the replication job is failing misserably upon inserts. This is due to more than likely to you having the “not for replication” setting on the subscriber table set to No. Best way to see all of this is to show you.

To create the scenario I'm trying to show let's create a few databases and setup replication.

```sql
CREATE DATABASE db1 
GO
ALTER DATABASE db1 SET RECOVERY SIMPLE
GO
CREATE DATABASE db2 
GO
ALTER DATABASE db2 SET RECOVERY SIMPLE
GO

USE db1
GO
CREATE TABLE tbl1 (ID INT IDENTITY(1,1) PRIMARY KEY,CharCol varchar(100))
GO

DECLARE @loop INT
SET @loop = 1

WHILE @loop <= 10
  BEGIN
	INSERT INTO tbl1 (CharCol) VALUES ('Some Text' + CAST(@loop as VarChar(6)))
	SET @loop = @loop + 1
 END
GO
Using the import wizard, import tbl1 into db1.  You could also simulate this by doing
USE db2
GO
CREATE TABLE tbl1 (ID INT,CharCol varchar(100))
GO
INSERT INTO tbl1
SELECT * FROM db1.dbo.tbl1
```
At this point you do not have a primary key or an identity seed on db2.tbl1. Go into design mode and flip the identity on and make the ID the primary key. This is the quickest way to accomplish that task without much effort for this test. Note: large tables of course this will take some time to run. Use your head when saying yes to those warnings. Leave not for replication to no so we can simulate the null affect of drop.

This was a quick way to get the table setup. You can also create the table with the primary key and identity as

```sql
USE [db2]
GO
CREATE TABLE [dbo].[tbl1](
	[ID] [int] IDENTITY(1,1) NOT FOR REPLICATION NOT NULL,
	[CharCol] [varchar](100) NULL,
 CONSTRAINT [PK_tbl1] PRIMARY KEY CLUSTERED 
(
	[ID] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
```
Then use identity_insert to bring over the data to sync it up. 

If you run this on db2.tbl1 after the data is in sync

```sql
DBCC CHECKIDENT
(
  tbl1
  ,NORESEED
)
```
After making all these changes, you will see that 10 is still our seed.
  
<span class="MT_red"><br /> Checking identity information: current identity value '10', current column value '10'.<br /> DBCC execution completed. If DBCC printed error messages, contact your system administrator.<br /> </span>
  
Let's setup replication and initialize everything to db2.tbl1

To setup the publisher execute the following

```sql
use db1
GO
exec sp_replicationdboption @dbname = N'db1', @optname = N'publish', @value = N'true'
GO
exec sp_addpublication 
@publication = N'Tables', 
@description = N'Transactional publication of database ''db1'' from Publisher ''LKFW00TK''.', 
@sync_method = N'concurrent', @retention = 0, @allow_push = N'true', 
@allow_pull = N'true', @allow_anonymous = N'true', 
@enabled_for_internet = N'false', @snapshot_in_defaultfolder = N'true', 
@compress_snapshot = N'false', @ftp_port = 21, 
@ftp_login = N'anonymous', @allow_subscription_copy = N'false', 
@add_to_active_directory = N'false', @repl_freq = N'continuous', 
@status = N'active', @independent_agent = N'true', 
@immediate_sync = N'true', @allow_sync_tran = N'false', 
@autogen_sync_procs = N'false', @allow_queued_tran = N'false', 
@allow_dts = N'false', @replicate_ddl = 1, @allow_initialize_from_backup = N'false', 
@enabled_for_p2p = N'false', @enabled_for_het_sub = N'false'
GO

exec sp_addpublication_snapshot @publication = N'Tables', @frequency_type = 1, 
@frequency_interval = 0, @frequency_relative_interval = 0, @frequency_recurrence_factor = 0, 
@frequency_subday = 0, @frequency_subday_interval = 0, @active_start_time_of_day = 0, 
@active_end_time_of_day = 235959, @active_start_date = 0, @active_end_date = 0, 
@job_login = null, @job_password = null, @publisher_security_mode = 1
GO

exec sp_addarticle @publication = N'Tables', @article = N'tbl1', @source_owner = N'dbo', 
@source_object = N'tbl1', @type = N'logbased', @description = N'', @creation_script = N'', 
@pre_creation_cmd = N'drop', @schema_option = 0x000000000803509F, 
@identityrangemanagementoption = N'manual', @destination_table = N'tbl1', 
@destination_owner = N'dbo', @status = 24, @vertical_partition = N'false', 
@ins_cmd = N'CALL [sp_MSins_dbotbl1]', @del_cmd = N'CALL [sp_MSdel_dbotbl1]', 
@upd_cmd = N'SCALL [sp_MSupd_dbotbl1]'
GO

exec sp_startpublication_snapshot @publication = 'Tables' 
GO

```
Now the first test subscriber

```sql
use [db2]
GO
exec sp_addpullsubscription 
@publisher = N'LKFW00TK', @publication = N'Tables', @publisher_db = N'db1', 
@independent_agent = N'True', @subscription_type = N'pull', @description = N'', 
@update_mode = N'read only', @immediate_sync = 1
GO

exec sp_addpullsubscription_agent 
@publisher = N'LKFW00TK', @publisher_db = N'db1', @publication = N'Tables', 
@distributor = N'LKFW00TK', @distributor_security_mode = 1, @distributor_login = N'', 
@distributor_password = N'', @enabled_for_syncmgr = N'False', @frequency_type = 64, 
@frequency_interval = 0, @frequency_relative_interval = 0, @frequency_recurrence_factor = 0, 
@frequency_subday = 0, @frequency_subday_interval = 0, @active_start_time_of_day = 0, 
@active_end_time_of_day = 235959, @active_start_date = 0, @active_end_date = 0, 
@alt_snapshot_folder = N'', @working_directory = N'', @use_ftp = N'False', @job_login = null, 
@job_password = null, @publication_type = 0
GO

use [db1]
exec sp_addsubscription 
@publication = N'Tables', @subscriber = N'LKFW00TK', 
@destination_db = N'db2', @sync_type = N'automatic', 
@subscription_type = N'pull', @update_mode = N'read only'
Go
```
This simulates the drop command and will initilize the subscription.

This gives us the synchronized data and pushing it across. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//rep_1.gif" alt="" title="" width="628" height="398" />
</div>

Launching the replication monitor will show the snapshot delivery

Test it out

```sql
INSERT INTO db1.dbo.tbl1 VALUES ('mytest 99')
```
Check db2.tbl1 to ensure your replication is functioning and the transaction pulled over. If you check the design of the table, you will see the drop functioned correctly and reset everything. This included the not for replication setting to yes.

Now check the seed 

```sql
USE db2
Go

DBCC CHECKIDENT
(
  tbl1
  ,NORESEED
)
```
<span class="MT_red">Checking identity information: current identity value 'NULL', current column value '11'.<br /> DBCC execution completed. If DBCC printed error messages, contact your system administrator.<br /> </span>
  
Or
  
<span class="MT_red"><br /> Checking identity information: current identity value 'NULL', current column value 'NULL'.<br /> DBCC execution completed. If DBCC printed error messages, contact your system administrator.<br /> </span>
  
As you can see the drop option basically reset our seed even while the seed is retained on the tables definition. If you attempt to insert directly into db2.tbl1 after removing replication you will violate the key.

You can prevent the Null from happening by using the delete option instead of drop. What this does not give you is your seed back. It will only delete the data in the subscriber table and bulk copy to initialize the table. The not for replication must still be set and if using the delete option you will need to manually set it on the subscriber table. What it does do for you is leave the seed at the last value used. That means if you delete your publisher and subscriber and set it back up using the delete option. 

You can simulate this all by using the exact script for the publication with the small change of @pre\_creation\_cmd = N'drop' to @pre\_creation\_cmd = N'delete'. 

Once you do the same steps as above and run DBCC CHECKIDENT after inserting some records to be replicated, you will see the identity value remain but the value rise.

Like this
  
<span class="MT_red"><br /> Checking identity information: current identity value '26', current column value '30'.<br /> DBCC execution completed. If DBCC printed error messages, contact your system administrator.<br /> </span>

I personally like this. It gives me point in time reference. You don't need this of course but it is nice to have with using the delete option.
  
So now the point of all of this is removing the subscriber. Go ahead and delete the subscriber from db2
  
e.g.

```sql
use [db2]
GO
exec sp_droppullsubscription @publisher = N'LKFW00TK', @publisher_db = N'db1', @publication = N'Tables'
GO
use [db1]
GO
exec sp_dropsubscription @publication =N'Tables', @subscriber = N'LKFW00TK', @article = N'all', @destination_db = N'db2'
```
Now insert a value into db2.tbl1
  
<span class="MT_red"><br /> Msg 2627, Level 14, State 1, Line 1<br /> Violation of PRIMARY KEY constraint 'PK_tbl1'. Cannot insert duplicate key in object 'dbo.tbl1'.<br /> The statement has been terminated.<br /> </span>
  
Of course this will happen sense your seed wants to use 27 in my results above. To fix this it's a very simple change to our DBCC CHECKIDENT command

```sql
DBCC CHECKIDENT
(
  tbl1
  ,RESEED
)
```
This goes ahead and grabs the highest value in the table and uses it to reseed the identity. Now if you insert into the table it will begin from 30 and you'll be good to go.

Reality is, you may never have to deal with this. Removing replication typically means the tables on the subscriber are no longer needed. There are cases of it though when the business or projects dictate the sites that you are replicating to for data support to applications become disconnected. That means they are on islands and replication isn't an option any longer. In order to have the applications function once again as separate entities, these types of modifications have to be accomplished to the tables.

Thanks goes out to [mrdenny][1] for the review of this blog.

 [1]: http://itknowledgeexchange.techtarget.com/sql-server/