---
title: Would you be interested in an information section for SQLCop?
author: SQLDenis
type: post
date: 2012-04-09T14:30:00+00:00
excerpt: |
  SQLCop detects the following issues right now. What else would you like to see?
  
  Detected issues
          
          Code
          
              Procedures with SP_
              VarChar Size Problems
              Decimal Size Problem
              Undocumented Procedures
              P&hellip;
url: /index.php/datamgmt/datadesign/would-you-be-interested-in/
views:
  - 6042
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - sql
  - sql server
  - sqlcop

---
George and I were thinking of adding a bunch of new checks/reports to [SQLCop][1]. SQLCop detects the following issues right now.

## Code

  * [Procedures with SP_][2]
  * [VarChar Size Problems][3]
  * [Decimal Size Problem][4]
  * [Undocumented Procedures][5]
  * Procedures without SET NOCOUNT ON
  * Procedures with SET ROWCOUNT
  * [Procedures with @@Identity][6]
  * Procedures with dynamic sql
  * Procedures using dynamic sql without sp_executesql

## Column

  * [Column Name Problems][7]
  * [Columns with float data type][8]
  * [Columns with image data type][9]
  * [Tables with text/ntext][9]
  * [Collation Mismatch][10]
  * [UniqueIdentifier with NewId][11]

## Table/Views

  * [Table Prefix][12]
  * [Table Name Problems][7]
  * [Missing Foreign Keys][13]
  * Wide Tables
  * [Tables without a primary key][14]
  * Empty Tables
  * [Views with order by][15]
  * Unnamed Constraints

## Indexes

  * [Fragmented indexes][16]
  * [Missing Foreign Key Indexes][17]
  * Forwarded Records

## Configuration

  * [Database Collation][18]
  * Auto Close
  * Auto Create
  * Auto Shrink
  * Auto Update
  * [Compatibility Level][19]
  * Login Language
  * Old Backups
  * [Orphaned Users][20]
  * [User Aliases][21]
  * Ad Hoc Distributed Queries
  * CLR
  * Database and log files on the same physical disk
  * Database Mail
  * Deprecated Features
  * Instant File Initialization
  * Max Degree of Parallelism
  * OLE Automation Procedures
  * Service Account
  * SMO and DMO
  * SQL Server Agent Service
  * xp cmdshell

## Health

  * Buffer cache hit ratio
  * Page life expectancy

I was thinking about adding an informational section to SQLCop and also some additional checks. Leave me a comment if you have other ideas or agree with the ones presented here

Would you like to know the last time DBCC ran successfully against your database?
  
This code would report that

<pre>CREATE TABLE #Test(ParentObject VARCHAR(500),Object VARCHAR(500),Field  VARCHAR(500),VALUE VARCHAR(500))

DECLARE @db VARCHAR(500)
SELECT @db =  db_name()

INSERT #Test
EXEC('DBCC DBINFO (''' + @db + ''') WITH TABLERESULTS')

SELECT DISTINCT Value FROM #Test
WHERE field = 'dbi_dbccLastKnownGood'

DROP TABLE #Test</pre>

If we add the configuration section would it be beneficial if we added the min and max values for the following

max worker threads
  
max text repl size (B)
  
max degree of parallelism
  
max server memory (MB)
  
max full-text crawl range

min memory per query (KB)
  
min server memory (MB)

For example

<pre>SELECT * FROM sys.configurations
WHERE name like( 'max%')

SELECT * FROM sys.configurations
WHERE name like( 'min%')</pre>

What about fillfactor?

<pre>SELECT * FROM sys.configurations
WHERE name  = 'fill factor (%)'</pre>

How about the 50 most used stored procedures

<pre>SELECT top 50 * FROM(SELECT COALESCE(OBJECT_NAME(s2.objectid),'Ad-Hoc') AS ProcName,execution_count,s2.objectid,
    (SELECT TOP 1 SUBSTRING(s2.TEXT,statement_start_offset / 2+1 ,
      ( (CASE WHEN statement_end_offset = -1
         THEN (LEN(CONVERT(NVARCHAR(MAX),s2.TEXT)) * 2)
         ELSE statement_end_offset END)  - statement_start_offset) / 2+1)) AS sql_statement,
       last_execution_time
FROM sys.dm_exec_query_stats AS s1
CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS s2 ) x
WHERE sql_statement NOT like 'SELECT * FROM(SELECT coalesce(object_name(s2.objectid)%'
and OBJECTPROPERTYEX(x.objectid,'IsProcedure') = 1
and exists (Select 1 from sys.procedures s
where s.is_ms_shipped = 0
and s.name = x.ProcName )
ORDER BY execution_count DESC</pre>

How about stored procedures with the highest average CPU time

<pre>SELECT top 50 * FROM(SELECT COALESCE(OBJECT_NAME(s2.objectid),'Ad-Hoc') AS ProcName,execution_count,s2.objectid,
    (SELECT TOP 1 SUBSTRING(s2.TEXT,statement_start_offset / 2+1 ,
      ( (CASE WHEN statement_end_offset = -1
         THEN (LEN(CONVERT(NVARCHAR(MAX),s2.TEXT)) * 2)
         ELSE statement_end_offset END)  - statement_start_offset) / 2+1)) AS sql_statement,
       s1.total_worker_time/s1.execution_count as AverageCPUTime
FROM sys.dm_exec_query_stats AS s1
CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS s2 ) x
WHERE sql_statement NOT like 'SELECT * FROM(SELECT coalesce(object_name(s2.objectid)%'
and OBJECTPROPERTYEX(x.objectid,'IsProcedure') = 1
and exists (Select 1 from sys.procedures s
where s.is_ms_shipped = 0
and s.name = x.ProcName )
ORDER BY AverageCPUTime DESC</pre>

Do you want to see all the different programs connected to your server?

<pre>SELECT COUNT(*) Count,program_name 
FROM sys.sysprocesses
WHERE status <&gt; 'background'
GROUP BY program_name
ORDER BY 1 desc</pre>

This returns something like this on one of my boxes

<pre>42	Microsoft SQL Server Management Studio - Query                                                                                  
40	Microsoft Visual FoxPro                                                                                                         
29	Microsoft SQL Server Management Studio                                                                                          
19	.Net SqlClient Data Provider                                                                                                    
2	Microsoft SQL Server                                                                                                            
1	SQLAgent - Alert Engine                                                                                                         
1	SQLAgent - Generic Refresher                                                                                                    
1	SQLAgent - Job invocation engine</pre>

Do you want to know the file size, space used and free space for all of the files for your database?

<pre>select
    CONVERT(DATE,GETDATE()) AS [Date],
    a.FILEID,
    [FILE_SIZE_MB] =
        convert(decimal(12,2),round(a.size/128.000,2)),
    [SPACE_USED_MB] =
        convert(decimal(12,2),round(fileproperty(a.name,'SpaceUsed')/128.000,2)),
    [FREE_SPACE_MB] =
        convert(decimal(12,2),round((a.size-fileproperty(a.name,'SpaceUsed'))/128.000,2)) ,
    NAME = left(a.NAME,35),
    FILENAME = left(a.FILENAME,60)
from
    dbo.sysfiles a</pre>

Here is what the output would look like

<pre>Date	   FILEID  FILE_SIZE_MB	 SPACE_USED  FREE_SPACE_MB  NAME	FILENAME
2012-04-09	1	1024.00	  1.56	     1022.44	   tempdev	D:MSSQLDatatempdb.mdf
2012-04-09	2	2048.00	  39.72	     2008.28	   templog	D:MSSQLDatatemplog.ldf
2012-04-09	3	1024.00	  0.25	     1023.75	   tempdev1	D:MSSQLDatatempdb1.ndf
2012-04-09	4	1024.00	  0.31	     1023.69	   tempdev2	D:MSSQLDatatempdb2.ndf
2012-04-09	5	1024.00	  0.38	     1023.63	   tempdev3	D:MSSQLDatatempdb3.ndf
2012-04-09	6	1024.00	  0.25	     1023.75	   tempdev4	D:MSSQLDatatempdb4.ndf
2012-04-09	7	1024.00	  0.25	     1023.75	   tempdev5	D:MSSQLDatatempdb5.ndf
2012-04-09	8	1024.00	  0.25	     1023.75	   tempdev6	D:MSSQLDatatempdb6.ndf
2012-04-09	9	1024.00	  0.44	     1023.56	   tempdev7	D:MSSQLDatatempdb7.ndf</pre>

Leave me a comment if you know of anything that you would like to add or if some of this stuff I listed would be useful

 [1]: http://sqlcop.ltd.local/
 [2]: /index.php/DataMgmt/DBProgramming/MSSQLServer/don-t-start-your-procedures-with-sp_
 [3]: /index.php/DataMgmt/DBProgramming/MSSQLServer/always-include-size-when-using-varchar-n
 [4]: /index.php/DataMgmt/DBProgramming/always-include-precision-and-scale-with
 [5]: /index.php/DataMgmt/DataDesign/identify-procedures-that-call-sql-server
 [6]: http://wiki.ltd.local/index.php/6_Different_Ways_To_Get_The_Current_Identity_Value
 [7]: /index.php/DataMgmt/DBProgramming/do-not-use-spaces-or-other-invalid-chara
 [8]: /index.php/DataMgmt/DBProgramming/do-not-use-the-float-data-type
 [9]: /index.php/DataMgmt/DBProgramming/don-t-use-text-datatype-for-sql-2005-and
 [10]: /index.php/DataMgmt/DBProgramming/sql-server-collation-conflicts
 [11]: /index.php/DataMgmt/DBProgramming/best-practice-don-t-not-cluster-on-uniqu
 [12]: /index.php/DataMgmt/DBProgramming/MSSQLServer/don-t-prefix-your-table-names-with-tbl
 [13]: /index.php/DataMgmt/DataDesign/missing-foreign-key-constraints
 [14]: /index.php/DataMgmt/DBProgramming/best-practice-every-table-should-have-a
 [15]: /index.php/DataMgmt/DataDesign/create-a-sorted-view-in-sql-server-2005--2008
 [16]: http://wiki.ltd.local/index.php/Finding_Fragmentation_Of_An_Index_And_Fixing_It
 [17]: http://www.jasonstrate.com/index.php/2010/06/index-those-foreign-keys/
 [18]: /index.php/DataMgmt/DBProgramming/collation-conflicts-with-temp-tables-and
 [19]: http://wiki.ltd.local/index.php/Database_compatibilty_level
 [20]: http://wiki.ltd.local/index.php/Fix_Orphaned_Database_Users
 [21]: http://www.mssqltips.com/tip.asp?tip=1675