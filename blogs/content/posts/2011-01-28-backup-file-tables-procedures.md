---
title: Backup file contents with SSIS – Supporting tables and procedures
author: Ted Krueger (onpnt)
type: post
date: 2011-01-28T13:07:00+00:00
excerpt: 'In part one and two we walked through design and the flow of the loop container for the package that will insert all files we specify into a SQL Server database.  This is being developed to automate the task of backing up and maintaining changes of configuration files that reside on servers.  This provides an easy and fast way to recover lost configuration files or use the configuration files during upgrades or migrations.'
url: /index.php/datamgmt/datadesign/backup-file-tables-procedures/
views:
  - 4838
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
In part [one][1] and [two][2] we walked through design and the flow of the loop container for the package that will insert all files we specify into a SQL Server database. This is being developed to automate the task of backing up and maintaining changes of configuration files that reside on servers. This provides an easy and fast way to recover lost configuration files or use the configuration files during upgrades or migrations. 

In this part of the series, the objects that are needed to support the SSIS package will be created and described. There will be three primary tables that are required and two procedures in order to update or insert new records into these tables. One of the tables mentioned was already described and the CREATE statement given in part two. That table is the staging table and it utilized only while the package is in runtime mode. 

Beyond the staging table, two tables will be combined to hold the information about the files themselves and then the contents of those files. These tables will be relational to each other based on the keys that are set in them. 

**The Master Table**

The master table will hold the primary information about the files. With any table design, the urge to simply place everything we need directly into one table is always there. This in some cases is optimal. OLAP systems for example benefit from this when reporting is the primary need. For our needs, the tables will be split into two tables so our main contents can be easily moved around. This is due to the storage needs that could arise from them. If this process was used by collecting from many servers, the contents table could very well become extremely large. Moving this table around can add some flexibility to managing it. 

The master table, SystemConfigMaster is below

<pre>CREATE TABLE [dbo].[SystemConfigMaster](
	[ConfigID] [int] IDENTITY(1,1) NOT NULL,
	[SystemName] [varchar](255) NULL,
	[ConfigDescription] [varchar](2500) NULL,
	[ContentType] [varchar](4) NULL,
	[CreateDate] [datetime] NULL,
	[SetupBy] [varchar](255) NULL,
	[ConfigPath] [varchar](1500) NULL,
 CONSTRAINT [PK__SysMaster_ConfigID] PRIMARY KEY CLUSTERED 
(
	[ConfigID] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY]

GO</pre>

ConfigID will be the primary key that will relate to the next table, ConfigRepository

<pre>CREATE TABLE [dbo].[ConfigRepository](
	[ContentConfigID] [int] IDENTITY(1,1) NOT NULL,
	[SystemConfigID] [int] NULL,
	[ConfigPath] [varchar](1500) NULL,
	[ImportBy] [varchar](255) NULL,
	[FileContents] [varchar](max) NULL,
 CONSTRAINT [PK__ConfigContent_ID] PRIMARY KEY CLUSTERED 
(
	[ContentConfigID] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY]

GO

SET ANSI_PADDING OFF
GO

ALTER TABLE [dbo].[ConfigRepository]  WITH CHECK ADD  CONSTRAINT [fk_MasterConfig] FOREIGN KEY([SystemConfigID])
REFERENCES [dbo].[SystemConfigMaster] ([ConfigID])
GO

ALTER TABLE [dbo].[ConfigRepository] CHECK CONSTRAINT [fk_MasterConfig]
GO</pre>

As shown, the ConfigRepository table has a Foreign Key to SystemConfigMaster and the ConfigID column. This requires an ID to be in place in SystemConfigMaster prior to inserting a row into ConfigRepository. 

**Stored Procedures**

To handle retaining integrity between these two tables, two procedures will be used. One procedure will be used to update the tables and another to insert a new row into them.
  
To get meaningful data into our tables, add two more variables to the package at this time: FileNameOnly and ServerName. Both of these variables will be string types. ServerName will later be used by passing in the actual server name that is being processed. This variable can be used in another higher processing task that can run through several servers. FileNameOnly is a performance addition. In the table, ConfigDescription will take the value of the file name only. This may be a temporary setting on the first processing of a server and can later be changed to a more meaningful or descriptive value manually. SystemName will be set to the ServerName variable.
  
To populate these new variables, change the initial script task to take the additional variable FileNameOnly as a ReadWriteVariable. Alter the coding to the following

<pre>if (File.Exists(Dts.Variables["IndexFile"].Value.ToString()))
            {
                FileInfo file = new FileInfo(Dts.Variables["IndexFile"].Value.ToString());
                Dts.Variables["FileModDate"].Value = Convert.ToDateTime(File.GetLastWriteTime(Dts.Variables["IndexFile"].Value.ToString()));
                Dts.Variables["FileNameOnly"].Value = file.Name.ToString();
                Dts.TaskResult = (int)ScriptResults.Success;
            }
            else
            {
                Dts.TaskResult = (int)ScriptResults.Failure;
            }</pre>

Now that the package is altered to process all the data required, create the actual procedures that will handle inserting the data.

**UDATE Procedure**

<pre>CREATE PROCEDURE [dbo].[dba_UpdateNewConfig] (@date datetime, @ConfigID INT,@ConfigContents varchar(max))
AS
SET NOCOUNT ON
UPDATE dbo.XMLConfigRepository
SET XMLContents = @ConfigContents
WHERE SystemConfigID = @ConfigID;


UPDATE dbo.SystemConfigMaster
SET CreateDate = @date
WHERE ConfigID = @ConfigID;
SET NOCOUNT OFF
GO</pre>

**INSERT Procedure**

<pre>CREATE PROCEDURE [dbo].[dba_InsertNewConfig] (@date datetime, @ConfigPath varchar(1500),@ConfigContents varchar(max), @Filename varchar(255), @ServerName varchar(255))
AS
SET NOCOUNT ON
DECLARE @IDENT INT

INSERT INTO SystemConfigMaster
(SystemName,ConfigDescription, ContentType, CreateDate, SetupBy, ConfigPath)
VALUES (@ServerName,@FileName,'Conf',@date,SUSER_NAME(),@ConfigPath)

SET @IDENT = (SELECT SCOPE_IDENTITY())

INSERT INTO dbo.XMLConfigRepository
SELECT @IDENT, ConfigPath, SUSER_NAME(),@ConfigContents FROM dbo.SystemConfigMaster
WHERE ConfigID = @IDENT
SET NOCOUNT OFF
GO</pre>

**Closing out the supporting objects**

With these two tables and procedures the Data Flow task development can start. In the last part to this series, Backup file contents with SSIS – INSERT/UPDATE Decisions, we will put together the Data Flow task and an Execute SQL Task to reset things for preparation of the next execution of the package.

 [1]: /index.php/DataMgmt/ssis-1/ssis-defining-the-design-1
 [2]: /index.php/DataMgmt/ssis-1/foreach-loop-container-and-file-handling