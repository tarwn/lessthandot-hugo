---
title: Security Auditing a Database
author: David Forck (thirster42)
type: post
date: 2010-10-08T16:57:48+00:00
ID: 919
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/security-auditing-a-server/
views:
  - 8325
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Earlier this week I decided that I wanted to generate a report that shows me how the security is set up on my databases. I'm mainly intersted in my database roles and how they're set up, and logins assigned to the database. So I did some snooping around and generated 3 queries for myself.

```sql
select
	dpr.name,
	dpr.principal_id,
	dpr.type,
	dpr.type_desc,
	dp.class,
	dp.class_desc,
	dp.major_id,
	dp.minor_id,
	dp.grantee_principal_id,
	dp.permission_name,
	case dp.state
		when 'g' then 'granted'
		when 'd' then 'denied'
		else 'n/a'
	end AS [state],
	ao.name as ObjectName,
	sc.name as SchemaName,
	case class_desc
		when 'OBJECT_OR_COLUMN' then ao.name
		when 'SCHEMA' then sc.name
	end AS PermissionObject
from sys.database_principals dpr
	left outer join sys.database_permissions dp
		on dpr.principal_id=dp.grantee_principal_id
	left outer join sys.all_objects ao
		on dp.major_id=ao.object_id
	left outer join sys.schemas sc
		on dp.major_id=sc.schema_id
where dpr.type='r'
	and class_desc>''
	and dpr.principal_id>0
order by name
```
This query displays all rights explicitly granted and denied in the database to database roles.

In this query, name is the name of the database role. Type and type\_desc describe what the grantee principal id is. class and class\_desc describes what is having a right granted to it. Permission_name shows the permission, and state shows if that permission is being granted or denied. Object name is the object that is having a right granted, schema name is the schema name that's being granted a right.

```sql
select
	dpr.name,
	dpr.principal_id,
	dpr.type,
	dpr.type_desc,
	dp.class,
	dp.class_desc,
	dp.major_id,
	dp.minor_id,
	dp.grantee_principal_id,
	dp.permission_name,
	case dp.state
		when 'g' then 'granted'
		when 'd' then 'denied'
		else 'n/a'
	end AS [state],
	ao.name as ObjectName,
	sc.name as SchemaName,
	case class_desc
		when 'OBJECT_OR_COLUMN' then ao.name
		when 'SCHEMA' then sc.name
	end AS PermissionObject
from sys.database_principals dpr
	left outer join sys.database_permissions dp
		on dpr.principal_id=dp.grantee_principal_id
	left outer join sys.all_objects ao
		on dp.major_id=ao.object_id
	left outer join sys.schemas sc
		on dp.major_id=sc.schema_id
where dpr.type in
(
	'S',
	'G',
	'U'
)
```
This query shows all of the rights explicitly granted/denied to logins.

```sql
SELECT 
	dpm.name as Member,
	dpg.name as Grp
  FROM ardb.[sys].[sysmembers] sm
	left outer join sys.database_principals dpm
		on sm.memberuid=dpm.principal_id
	left outer join sys.database_principals dpg
		on sm.groupuid=dpg.principal_id
```
This query shows all of the database roles and all of the logins assigned to the roles. Grp is the database role and Member is the login name.

Now, these three queries gave me a good starting point, but to run these you have to go to each database. And if i want to query different servers (i've got 3, a dev, a test, and a prod) to make sure they're similer, i'd have to connect to each server. So, I created a couple of stored procedures and threw those into a reporting services report. Now, i can go to the report, query a sever and a database, and then compare it to another server and database.

here's the script to create the sp's that i made.

```sql
-- =============================================
-- Author:		David Forck DF
-- Create date: 01Oct10
-- Description:	Report to show database role membership
-- =============================================
CREATE PROCEDURE [dbo].[RoleMembership]
	-- Add the parameters for the stored procedure here
	@servername varchar(max),
	@databasename nvarchar(max)
AS
BEGIN
	-- SET NOCOUNT ON added to prevent extra result sets from
	-- interfering with SELECT statements.
	SET NOCOUNT ON;

    -- Insert statements for procedure here




declare @sql varchar(max), 
	@database varchar(max), 
	@server varchar(max), 
	@string varchar(max)



select @server= name from sys.servers where name=@servername

select @database= name from sys.databases where name=@databasename

set @string=@server+'.'+@database



set @sql='
SELECT 
	dpm.name as Member,
	dpg.name as Grp
  FROM ' + @string + '.[sys].[sysmembers] sm
	left outer join ' + @string + '.sys.database_principals dpm
		on sm.memberuid=dpm.principal_id
	left outer join ' + @string + '.sys.database_principals dpg
		on sm.groupuid=dpg.principal_id
'

--print @databasename
--print @sql

exec(@sql)






END


GO


-- =============================================
-- Author:		David Forck DF
-- Create date: 01Oct10
-- Description:	security report for logins
-- =============================================
CREATE PROCEDURE [dbo].[LoginSecurity]
	-- Add the parameters for the stored procedure here
	@servername varchar(max),
	@databasename nvarchar(max)
	
AS
BEGIN
	-- SET NOCOUNT ON added to prevent extra result sets from
	-- interfering with SELECT statements.
	SET NOCOUNT ON;

    -- Insert statements for procedure here



declare @sql varchar(max), 
	@database varchar(max), 
	@server varchar(max), 
	@string varchar(max)

select @server= name from sys.servers where name=@servername

select @database= name from sys.databases where name=@databasename

set @string=@server+'.'+@database


set @sql='
select
	dpr.name,
	dpr.principal_id,
	dpr.type,
	dpr.type_desc,
	dp.class,
	dp.class_desc,
	dp.major_id,
	dp.minor_id,
	dp.grantee_principal_id,
	dp.permission_name,
	case dp.state
		when ''g'' then ''granted''
		when ''d'' then ''denied''
		else ''n/a''
	end AS [state],
	ao.name as ObjectName,
	sc.name as SchemaName,
	case class_desc
		when ''OBJECT_OR_COLUMN'' then ao.name
		when ''SCHEMA'' then sc.name
	end AS PermissionObject
from ' + @string + '.sys.database_principals dpr
	left outer join ' + @string + '.sys.database_permissions dp
		on dpr.principal_id=dp.grantee_principal_id
	left outer join ' + @string + '.sys.all_objects ao
		on dp.major_id=ao.object_id
	left outer join ' + @string + '.sys.schemas sc
		on dp.major_id=sc.schema_id
where dpr.type in
(
	''S'',
	''G'',
	''U''
)
'

--print @databasename
--print @sql

exec(@sql)

END




-- =============================================
-- Author:		David Forck DF
-- Create date: 01Oct10
-- Description:	Stored procedure that generates a security report per database for database roles
-- =============================================
CREATE PROCEDURE [dbo].[DatabaseRoleSecurity]
	-- Add the parameters for the stored procedure here
	@servername varchar(max),
	@databasename varchar(max)
	
AS
BEGIN
	-- SET NOCOUNT ON added to prevent extra result sets from
	-- interfering with SELECT statements.
	SET NOCOUNT ON;

    -- Insert statements for procedure here


declare @sql varchar(max), 
	@database varchar(max), 
	@server varchar(max), 
	@string varchar(max)


select @server= name from sys.servers where name=@servername

select @database= name from sys.databases where name=@databasename

set @string=@server+'.'+@database


set @sql='

select
	dpr.name,
	dpr.principal_id,
	dpr.type,
	dpr.type_desc,
	dp.class,
	dp.class_desc,
	dp.major_id,
	dp.minor_id,
	dp.grantee_principal_id,
	dp.permission_name,
	case dp.state
		when ''g'' then ''granted''
		when ''d'' then ''denied''
		else ''n/a''
	end AS [state],
	ao.name as ObjectName,
	sc.name as SchemaName,
	case class_desc
		when ''OBJECT_OR_COLUMN'' then ao.name
		when ''SCHEMA'' then sc.name
	end AS PermissionObject
from ' + @string + '.sys.database_principals dpr
	left outer join ' + @string + '.sys.database_permissions dp
		on dpr.principal_id=dp.grantee_principal_id
	left outer join ' + @string + '.sys.all_objects ao
		on dp.major_id=ao.object_id
	left outer join ' + @string + '.sys.schemas sc
		on dp.major_id=sc.schema_id
where dpr.type=''r''
	and class_desc>''''
	and dpr.principal_id>0
order by name'
	

--print @database
--print @server
--print @string
--print @sql

exec(@sql)


END


GO

```
Now, if you get to looking, the verification of the server name and database is actually running on the server that's hosting the sp's. I could have spent some more time and changed this to do this dynamically, but most of my servers have a similer database list. So, keep that in mind if you use these.