---
title: How to get information about all databases without a loop
author: Naomi Nosonovsky
type: post
date: 2011-11-30T12:47:00+00:00
excerpt: 'Quite often we want to consolidate query information across all databases (or all user databases). When this question is asked in forums, the usual recommendation is to either try running undocumented sp_MSForEachDB stored procedure or do a loop and use&hellip;'
url: /index.php/datamgmt/datadesign/how-to-get-information-about-all-databas/
views:
  - 128806
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Quite often we want to consolidate query information across all databases (or all user databases). When this question is asked in forums, the usual recommendation is to either try running [undocumented sp_MSForEachDB stored procedure][1] or do a loop and use dynamic SQL. The example of such stored procedures can be found, for example, [THIRD EXAMPLE &#8211; SPROC to enumerate all objects in databases][2].

The idea occurred to me last night that while we do need a dynamic SQL to solve this problem, we don&#8217;t really need a loop unless we need a second loop involving looping through all tables.

### Table of Contents:

[Indexes in all databases with their usage][3]
  
[Indexes in all databases with their physical stats][4]
  
[Count of all objects in all databases][5]
  
[Record Count in every table in a database][6]
  
[Quick Record Count in All Tables in All Databases][7]
  
[Quick Record Count in All Tables in All Databases (2)][8]
  
[Sizes of All Tables in a Database][9]
  
[Database Files Sizes in All Databases][10]
  
[Database Files Sizes in All Databases and used space][11]
  
[Backup All Databases with Compression (SQL 2008+)][12]
  
[All Schema Names in All Databases][13]
  
[List of All Tables in All Databases][14]
  
[List of All Tables in All Databases (2)][15]
  
[List of all Stored Procedures in All Databases][16]
  
[Database Files Growth][17]

Here is a script demonstrating this idea &#8211; it lists indexes in all databases with their usage. This script was an answer to [Help in T-SQL Query thread][18] in MSDN T-SQL forum.

## Indexes in all databases with their usage {#1}

<pre>declare @SQL nvarchar(max)
if object_id('tempdb..#Result','U') IS not NULL
 drop table #Result
create table #Result (DBName sysname, TableName Sysname, IndexName sysname, Usage bigint)

select @SQL = coalesce(@SQL,'') + CHAR(13) + CHAR(10) + ' use ' + QUOTENAME([Name]) + ';
insert into #Result select ' + quotename([Name],'''') + ' as DbName, 
object_name(i.object_id) as tablename,  i.name as indexname, 
s.user_seeks + s.user_scans + s.user_lookups + s.user_updates as usage
from sys.indexes i   
inner join sys.dm_db_index_usage_stats s        
on s.object_id = i.object_id                    
and s.index_id = i.index_id              
and s.database_id = db_id()
where objectproperty(i.object_id, ''IsUserTable'') = 1   
and i.index_id &gt; 0 
order by usage;' from sys.databases 
--print (@SQL)
execute (@SQL)
select * from #Result order by [DbName],[Usage]
drop table #Result</pre>

## Indexes in all databases with their physical stats {#2}

<pre>declare @SQL nvarchar(max)
set @SQL = ''
select @SQL = @SQL +
'Select ' + quotename(name,'''') + ' as [DB Name], 
object_Name(PS.Object_ID,' + convert(varchar(10),database_id) + ') as [Object],
I.Name as [Index Name], PS.Partition_Number, PS.Index_Type_Desc, 
PS.alloc_unit_type_desc,	PS.index_depth,	PS.index_level,
PS.avg_fragmentation_in_percent,	PS.fragment_count,	PS.avg_fragment_size_in_pages,
PS.page_count,	PS.avg_page_space_used_in_percent,	PS.record_count,	
PS.ghost_record_count,	PS.version_ghost_record_count,
PS.min_record_size_in_bytes,	PS.max_record_size_in_bytes,	PS.avg_record_size_in_bytes,
PS.forwarded_record_count,	PS.compressed_page_count
 from ' + quotename(name) + '.sys.dm_db_index_physical_stats(' + 
convert(varchar(10),database_id) + ', NULL, NULL, NULL, NULL) PS 
INNER JOIN ' + quotename(name) + 
'.sys.Indexes I on PS.Object_ID = I.Object_ID and PS.Index_ID = I.Index_ID ' 
+ CHAR(13)

 from sys.databases where state_desc = 'ONLINE'

execute(@SQL)</pre>

Another example of the same idea you can find in [Finding Record with Last Modified date in all tables][19]

Using this same idea you can get a count of all objects in all your databases using this PIVOT query:

## Count of all objects in all databases {#3}

<pre>declare @Qry nvarchar(max) 
select @Qry = coalesce(@Qry + char(13) + char(10) + ' UNION ALL ','') + '
select ' + quotename([Name],'''') + ' as DBName, [AGGREGATE_FUNCTION], [CHECK_CONSTRAINT],[DEFAULT_CONSTRAINT],

[FOREIGN_KEY_CONSTRAINT],

[SQL_SCALAR_FUNCTION],

[CLR_SCALAR_FUNCTION],

[CLR_TABLE_VALUED_FUNCTION],

[SQL_INLINE_TABLE_VALUED_FUNCTION],

[INTERNAL_TABLE],[SQL_STORED_PROCEDURE],[CLR_STORED_PROCEDURE],[PLAN_GUIDE],[PRIMARY_KEY_CONSTRAINT],
[RULE],[REPLICATION_FILTER_PROCEDURE],[SYNONYM],[SERVICE_QUEUE],[CLR_TRIGGER],[SQL_TABLE_VALUED_FUNCTION],[SQL_TRIGGER],
[TABLE_TYPE],[USER_TABLE],[UNIQUE_CONSTRAINT],[VIEW],[EXTENDED_STORED_PROCEDURE]

from (select [Name], type_Desc from ' + quotename([Name]) + '.sys.objects where is_ms_shipped = 0) src 
PIVOT (count([Name]) FOR type_desc in ([AGGREGATE_FUNCTION], [CHECK_CONSTRAINT],[DEFAULT_CONSTRAINT],

[FOREIGN_KEY_CONSTRAINT],

[SQL_SCALAR_FUNCTION],

[CLR_SCALAR_FUNCTION],

[CLR_TABLE_VALUED_FUNCTION],

[SQL_INLINE_TABLE_VALUED_FUNCTION],

[INTERNAL_TABLE],[SQL_STORED_PROCEDURE],[CLR_STORED_PROCEDURE],[PLAN_GUIDE],[PRIMARY_KEY_CONSTRAINT],
[RULE],[REPLICATION_FILTER_PROCEDURE],[SYNONYM],[SERVICE_QUEUE],[CLR_TRIGGER],[SQL_TABLE_VALUED_FUNCTION],[SQL_TRIGGER],
[TABLE_TYPE],[USER_TABLE],[UNIQUE_CONSTRAINT],[VIEW],[EXTENDED_STORED_PROCEDURE])) pvt' from sys.databases 
where [name] NOT IN ('master','tempdb','model','msdb') order by [Name]

execute(@Qry)</pre>

You can only list types you&#8217;re interested in, of course.

The script below will give you a count of records in every table in a database:

## Record Count in every table in a database {#4}

<pre>DECLARE  @DynamicSQL NVARCHAR(MAX)
 
SELECT   @DynamicSQL = COALESCE(@DynamicSQL + CHAR(13) + ' UNION ALL ' + CHAR(13),
                                '') + 
                                'SELECT ' + quotename(table_schema,'''') + ' as [Schema Name], ' +
                                QUOTENAME(TABLE_NAME,'''') + 
                                ' as [Table Name], COUNT(*) AS [Records Count] FROM ' + 
                                quotename(Table_schema) + '.' + QUOTENAME(TABLE_NAME)
FROM     INFORMATION_SCHEMA.TABLES
ORDER BY TABLE_NAME
 
--print (@DynamicSQL) -- we may want to use PRINT to debug the SQL
EXEC( @DynamicSQL)</pre>

Quick row count in all tables in all databases in the server instance (you can exclude system databases from that loop, obviously)

## Quick Record Count in All Tables in All Databases {#5}

<pre>declare @SQL nvarchar(max)

set @SQL = ''
--select * from sys.databases 
select @SQL = @SQL + CHAR(13) + 'USE ' + QUOTENAME([name]) + ';
SELECT ' +quotename([name],'''') + 'as [Database Name], 
   SchemaName=s.name
  ,TableName=t.name
  ,CreateDate=t.create_date
  ,ModifyDate=t.modify_date
  ,p.rows
  ,DataInKB=sum(a.used_pages)*8
FROM sys.schemas s
JOIN sys.tables t on s.schema_id=t.schema_id
JOIN sys.partitions p on t.object_id=p.object_id
JOIN sys.allocation_units a on a.container_id=p.partition_id
GROUP BY s.name, t.name, t.create_date, t.modify_date, p.rows
ORDER BY SchemaName, TableName' from sys.databases  

execute (@SQL)</pre>

Another way with less info:

## Quick Record Count in All Tables in All Databases {#6}

<pre>declare @SQL nvarchar(max)

set @SQL = ''
--select * from sys.databases 
select @SQL = @SQL + CHAR(13) + 'USE ' + QUOTENAME([name]) + ';
SELECT ' +quotename([name],'''') + 'as [Database Name], so.name AS [Table Name],   
    rows AS [RowCount]   
FROM sysindexes AS si   
    join sysobjects AS so on si.id = so.id   
WHERE indid IN (0,1)   
    AND xtype = ''U''' from sys.databases  

execute (@SQL)    </pre>

Here is a script showing sizes from all tables in a database.

## Sizes of All Tables in a Database {#7}

<pre>--exec sp_MSforeachtable 'print ''?'' exec sp_spaceused ''?'''
if OBJECT_ID('tempdb..#TablesSizes') IS NOT NULL
   drop table #TablesSizes
   
create table #TablesSizes (TableName sysname, Rows bigint, reserved varchar(100), data varchar(100), index_size varchar(100), unused varchar(100))

declare @sql varchar(max)
select @sql = coalesce(@sql,'') + '
insert into #TablesSizes execute sp_spaceused ' + QUOTENAME(Table_Name,'''') from INFORMATION_SCHEMA.TABLES

--print (@SQL)
execute (@SQL)

select * from #TablesSizes order by TableName</pre>

Here is a script showing database files sizes for all databases

Before I show the T-SQL code I&#8217;d like to point to this [very interesting blog][20] as how to get database sizes in all SQL Server instances using PowerShell

## Database Files Sizes in All Databases {#8}

<pre>create  table #FileSizes (DBName sysname, [File Name] varchar(max), [Physical Name] varchar(max),
Size decimal(12,2))
declare @SQL nvarchar(max)
set @SQL = ''
select @SQL = @SQL + 'USE'  + QUOTENAME(name) + '
insert into #FileSizes
select ' + QUOTENAME(name,'''') + ', Name, Physical_Name, size/1024.0 from sys.database_files ' 
from sys.databases

execute (@SQL)
select * from #FileSizes order by DBName, [File Name]</pre>

&nbsp;

You can also find the script to show all database sizes using sp_MsForEachDB [here][21]. See also this [relevant thread][22] at MSDN.

<pre>declare @Sql varchar(max)
select @SQL =coalesce(@SQL + char(13) + 'UNION ALL 
' ,'') + 'SELECT ''' + name + ''' AS DBNAME,' + 
'sum(size * 8 /1024.0) AS MB from ' + quotename(name) + '.dbo.sysfiles' 
from sys.databases
order by name

execute (@SQL)</pre>

&nbsp;

## Database Files Sizes in All Databases and used space {#9}

Note, that this script assumes that database files have the same name as the database itself. If this is not true, this script will not return correct result.

<pre>create table #Test (DbName sysname, TotalSize decimal(20,2), Used decimal(20,2), [free space percentage] decimal(20,2))

declare @SQL nvarchar(max)
select @SQL = coalesce(@SQL,'') + 
'USE ' + QUOTENAME(Name) + '
insert into #Test
select DB.name, ssf.size*8 as total, 
FILEPROPERTY (AF.name, ''spaceused'')*8 as used, 
((ssf.size*8) - (FILEPROPERTY (AF.name, ''spaceused'')*8))*100/(ssf.size*8) as [free space percentage]
from sys.sysALTfiles AF 
inner join sys.sysfiles ssf on ssf.name=AF.name COLLATE SQL_Latin1_General_CP1_CI_AS
INNER JOIN sys.databases DB ON AF.dbid=DB.database_id 
where ssf.groupid<&gt;1' from sys.databases

execute(@SQL)

select * from #Test order by DbName </pre>

This script will backup all databases (using compression):

## Backup All Databases with Compression (SQL 2008+) {#10}

<pre>Declare @ToExecute nvarChar(max);
declare @cBackupPath nvarchar(max) = N'D:\SQL Server Databases\SQL 2014\Backup';

Select @ToExecute = (select CHAR(13) + CHAR(10) + CHAR(13) + CHAR(10) + 'Backup Database ' + quotename([Name]) +
' To Disk = ' + quotename(@cBackupPath + [Name] + '.bak','''') + '
WITH NOFORMAT, NOINIT,
SKIP, NOREWIND, NOUNLOAD, COMPRESSION, STATS = 10;'

From sys.databases

Where [Name] Not In ('tempdb') and databasepropertyex ([Name],'Status') = 'online'
order by [name]

for xml path(''), type).value('.', 'nvarchar(max)');

print @ToExecute

Execute (@ToExecute)</pre>

&nbsp;

## All Schema Names in All Databases {#11}

<pre>declare @Sql nvarchar(max)
create table AllDBSchemas ([DB Name] sysname, [Schema Name] sysname)

select @Sql = coalesce(@Sql,'') + '
insert into AllDBSchemas

select ' + QUOTENAME(name,'''') + ' as [DB Name], [Name] as [Schema Name] from ' + 
QUOTENAME(Name) + '.sys.schemas order by [DB Name],[Name];' from sys.databases
order by name

execute(@SQL)

select * from AllDBSchemas order by [DB Name],[SCHEMA NAME]</pre>

&nbsp;

## List of All Tables in All Databases {#12}

<pre>CREATE TABLE AllTables ([DB Name] sysname, [Schema Name] sysname, [Table Name] sysname)

DECLARE @SQL NVARCHAR(MAX)
 
SELECT @SQL = COALESCE(@SQL,'') + '
insert into AllTables
 
select ' + QUOTENAME(name,'''') + ' as [DB Name], [Table_Schema] as [Table Schema], [Table_Name] as [Table Name] from ' +
QUOTENAME(Name) + '.INFORMATION_SCHEMA.Tables;' FROM sys.databases
ORDER BY name
 
EXECUTE(@SQL)
 
SELECT * FROM AllTables ORDER BY [DB Name],[SCHEMA NAME], [Table Name]</pre>

Alternative way to get all tables in all databases:

## List of All Tables in All Databases {#13}

<pre>if object_ID('TempDB..#AllTables','U') IS NOT NULL drop table #AllTables
CREATE TABLE #AllTables ([DB Name] sysname, [Schema Name] nvarchar(128) NULL, [Table Name] sysname, create_date datetime, modify_date datetime)

DECLARE @SQL NVARCHAR(MAX)
 
SELECT @SQL = COALESCE(@SQL,'') + 'USE ' + quotename(name) + '
insert into #AllTables 
select ' + QUOTENAME(name,'''') + ' as [DB Name], schema_name(schema_id) as [Table Schema], [Name] as [Table Name], Create_Date, Modify_Date
 from ' +
QUOTENAME(Name) + '.sys.Tables;' FROM sys.databases
ORDER BY name
--print @SQL 
EXECUTE(@SQL)</pre>

## List of all Stored Procedures in All Databases {#14}

<pre>create table #SPList ([DB Name] sysname, [SP Name] sysname, create_date datetime, modify_date datetime)

declare @SQL nvarchar(max)
set @SQL = ''
select @SQL = @SQL + ' insert into #SPList 
select ' + QUOTENAME(name, '''') + ', name, create_date, modify_date
from ' + QUOTENAME(name) + '.sys.procedures' from sys.databases

execute (@SQL)

select * from #SPList order by [DB Name], [SP Name]</pre>

## Database Files Growth {#15}

<pre>--select * from sys.sysfiles  

declare @SQL nvarchar(max)
select @SQL = coalesce(@SQL + '
UNION ALL ','') + 

'SELECT CONVERT(varchar(100),
SERVERPROPERTY(''Servername'')) AS Server, ' + 
quotename(name,'''') +'  as DatabaseName,
    CAST(name as varchar(128)) COLLATE SQL_Latin1_General_CP1_CI_AS as name,
    CAST(filename as varchar(128)) COLLATE SQL_Latin1_General_CP1_CI_AS as FileName,
    Autogrowth = ''Autogrowth: ''
        +
        CASE
            WHEN (status & 0x100000 = 0 AND CEILING((growth * 8192.0) / (1024.0 * 1024.0)) = 0.00) OR growth = 0 THEN ''None''
            WHEN status & 0x100000 = 0 THEN ''By '' + 
            CONVERT(VARCHAR,CEILING((growth * 8192.0) / (1024.0 * 1024.0))) + '' MB''
            ELSE ''By '' + CONVERT(VARCHAR,growth) + '' percent''
        END
        +
        CASE
            WHEN (status & 0x100000 = 0 AND CEILING((growth * 8192.0) / (1024.0 * 1024.0)) = 0.00) OR growth = 0 THEN ''''
            WHEN CAST([maxsize] * 8.0 / 1024 AS DEC(20,2)) <= 0.00 THEN '', unrestricted growth''
            ELSE '', restricted growth to '' + CAST(CAST([maxsize] * 8.0 / 1024 AS DEC(20)) AS VARCHAR) + '' MB''
        END
FROM '  + quotename(name) + '.sys.sysfiles  s'
from sys.databases

set @SQL = @SQL + ' 
ORDER BY DatabaseName'

print @SQL

execute(@SQL)</pre>

The possibilities are endless.

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][23] forum or our [Microsoft SQL Server Admin][24] forum.**<ins></ins>

 [1]: http://www.mssqltips.com/tip.asp?tip=1905&ctc
 [2]: http://sqlusa.com/bestpractices/training/scripts/dynamicsql/
 [3]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#1
 [4]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#2
 [5]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#3
 [6]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#4
 [7]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#5
 [8]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#6
 [9]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#7
 [10]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#8
 [11]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#9
 [12]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#10
 [13]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#11
 [14]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#12
 [15]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#13
 [16]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#14
 [17]: /index.php/DataMgmt/DataDesign/how-to-get-information-about-all-databas#15
 [18]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/ab4b39b0-5bb2-4ffc-a25d-6bb8fe6124ef/#f3a3a011-4318-44e2-97c1-5bdadd7b2444
 [19]: http://wiki.ltd.local/index.php/Finding_Record_with_Last_Modified_date_in_all_tables
 [20]: http://blogs.technet.com/b/heyscriptingguy/archive/2010/11/02/use-powershell-to-obtain-sql-server-database-sizes.aspx
 [21]: http://www.kodyaz.com/articles/list-database-size-using-sql-server-sp_msforeachdb.aspx
 [22]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/226bbffc-2cfa-4fa8-8873-48dec6b5f17f
 [23]: http://forum.lessthandot.com/viewforum.php?f=17
 [24]: http://forum.lessthandot.com/viewforum.php?f=22