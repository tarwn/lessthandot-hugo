---
title: 'SQL Advent 2011 Day 22: Dynamic Management Views'
author: SQLDenis
type: post
date: 2011-12-22T08:52:00+00:00
ID: 1459
excerpt: |
  Today we are going to take a look at Dynamic Management Views. Dynamic Management Views is one of my favorite things that they have added to the SQL Server product. Instead of running all kinds of stored procedures, dbcc commands and selects from table, you can get all that information now from the Dynamic Management Views, all you need to know is what view will get you the information you need.
        
        
          
            I/O Related Dynamic Management Views and Functions
          
        
      
      
        
          
            Change Tracking Related Dynamic Management&hellip;
url: /index.php/datamgmt/datadesign/dynamic-management-views/
views:
  - 6645
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - dmv
  - dynamic management views
  - profiling
  - sql server 2005
  - sql server 2008

---
In my [Are you ready for SQL Server 2012 or are you still partying like it is 1999?][1] post, I wrote about how you should start using SQL Server 2005 and SQL Server 2008 functionality now in order to prepare for SQL Server 2012. I still see tons of code that is written in the pre 2005 style and people still keep using those functions, procs and statements even though SQL Server 2005 and 2008 have much better functionality.

Today we are going to take a look at Dynamic Management Views. Dynamic Management Views is one of my favorite things that they have added to the SQL Server product. Instead of running all kinds of stored procedures, dbcc commands and selects from table, you can get all that information now from the Dynamic Management Views, all you need to know is what view will get you the information you need.

Let&#8217;s take a look at some examples. This query below will give me the top 50 most executed statements in stored procedures

sql
SELECT TOP 50 * FROM(SELECT COALESCE(OBJECT_NAME(s2.objectid),'Ad-Hoc') AS ProcName,execution_count,s2.objectid,
    (SELECT TOP 1 SUBSTRING(s2.TEXT,statement_start_offset / 2+1 ,
      ( (CASE WHEN statement_end_offset = -1
         THEN (LEN(CONVERT(NVARCHAR(MAX),s2.TEXT)) * 2)
         ELSE statement_end_offset END)  - statement_start_offset) / 2+1)) AS sql_statement,
       last_execution_time
FROM sys.dm_exec_query_stats AS s1
CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS s2 ) x
WHERE sql_statement NOT like 'SELECT * FROM(SELECT coalesce(object_name(s2.objectid)%'
and OBJECTPROPERTYEX(x.objectid,'IsProcedure') = 1
and exists (SELECT 1 FROM sys.procedures s
WHERE s.is_ms_shipped = 0
and s.name = x.ProcName )
ORDER BY execution_count DESC
```

Here is some sample output

<pre>proc            count           objectid        statement               last_execution_time
----------------------------------------------------------------------------------------------
usp_AddLast	42757230	517576882	UPDATE dbo.LastSome	2010-04-14 14:15:33.433
usp_AddLast	42757230	517576882	IF EXISTS(   SELECT 	2010-04-14 14:15:33.433
usp_Update	20290	        725577623	update t  set pclose	2010-04-14 14:15:33.433
usp_Update	20288	        725577623	update t  set pclose	2010-04-14 14:15:33.453
usp_GetLast3	3188	        501576825	SELECT distinct l.Sy	2010-04-14 14:14:33.350
usp_Historical	168	        1029578706	select * from Histor	2010-04-14 14:02:08.190
usp_AddLast	3	        517576882	INSERT dbo.Sometable	2010-04-07 08:42:57.040
usp_GetLast2	3	        965578478	SELECT l.Symbol,   q	2010-04-06 16:14:49.523
usp_Historical2	2	        1045578763	select SomeNumber,G	2010-04-14 12:10:48.860
usp_GetLast	1	        901578250	SELECT distinct l.Sy	2010-02-12 09:11:59.840</pre>

As you can see the first two rows are for the same stored procedure, what if you only want to know the procedure names? You can use the following query for that, I grouped them by name and then used the max count of the statement itself as the execution count, you could also use SUM instead of MAX. If you have a lot of if else conditions then max might not give you the whole picture.

sql
SELECT TOP 50 * FROM
    (SELECT OBJECT_NAME(s2.objectid) AS ProcName,
        MAX(execution_count) AS execution_count,s2.objectid,
        MAX(last_execution_time) AS last_execution_time
FROM sys.dm_exec_query_stats AS s1
CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS s2
GROUP BY OBJECT_NAME(s2.objectid),s2.objectid) x
WHERE OBJECTPROPERTYEX(x.objectid,'IsProcedure') = 1
AND EXISTS (SELECT 1 FROM sys.procedures s
            WHERE s.is_ms_shipped = 0
            AND s.name = x.ProcName )
ORDER BY execution_count DESC
```

Here is the output

<pre>proc            count           objectid        last_execution_time
----------------------------------------------------------------------
usp_AddLast	42667941	517576882	2010-04-14 14:08:48.287
usp_Update	20263		725577623	2010-04-14 14:08:48.307
usp_GetLast3	3180		501576825	2010-04-14 14:07:10.513
usp_Historical	168		1029578706	2010-04-14 14:02:08.190
usp_GetLast2	3		965578478	2010-04-06 16:14:49.523
usp_Historical2	2		1045578763	2010-04-14 12:10:48.860
usp_GetLast	1		901578250	2010-02-12 09:11:59.840</pre>



Imagine doing stuff like this in the SQL Server 2000 days&#8230;..better get profiler and some traces running.

What if you want to know the stored procedures with the highest average CPU time in SQL Server? That is pretty easy as well, here is the query for that

sql
SELECT TOP 50 * FROM
    (SELECT OBJECT_NAME(s2.objectid) AS ProcName,
        SUM(s1.total_worker_time/s1.execution_count) AS AverageCPUTime,s2.objectid,
        SUM(execution_count) AS execution_count
FROM sys.dm_exec_query_stats AS s1
CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS s2
GROUP BY OBJECT_NAME(s2.objectid),objectid) x
WHERE OBJECTPROPERTYEX(x.objectid,'IsProcedure') = 1
AND EXISTS (SELECT 1 FROM sys.procedures s
            WHERE s.is_ms_shipped = 0
            AND s.name = x.ProcName )
ORDER BY AverageCPUTime DESC
```

Output

<pre>proc            AverageCPUTime objectid    execution_count
----------------------------------------------------------------------
usp_Update	17152	       725577623	41074
usp_GetLast3	333		965578478	3
usp_GetLast2	145		501576825	3237
usp_Historical	70		1029578706	170
usp_AddLast	36		517576882	87154735
usp_GetLast	0		901578250	1
usp_Historical2	0		1045578763	2</pre>

Wow, that usp_Update proc really sucks 🙂

Here is another one of my favorite queries. How long will the database restore take?
  
Run the query below and you will know

sql
SELECT
    d.PERCENT_COMPLETE AS [%Complete],
    d.TOTAL_ELAPSED_TIME/60000 AS ElapsedTimeMin,
    d.ESTIMATED_COMPLETION_TIME/60000   AS TimeRemainingMin,
    d.TOTAL_ELAPSED_TIME*0.00000024 AS ElapsedTimeHours,
    d.ESTIMATED_COMPLETION_TIME*0.00000024  AS TimeRemainingHours,
    s.TEXT AS Command
FROM    sys.dm_exec_requests d
CROSS APPLY sys.dm_exec_sql_text(d.sql_handle)AS s
WHERE  d.COMMAND LIKE 'RESTORE DATABASE%'
ORDER   BY 2 DESC, 3 DESC
```

For all the sessions that are connected, what state are they in? The query below will give you a quick count

sql
SELECT COUNT(*) AS StatusCount,CASE status
WHEN 'Running' THEN 'Running - Currently running one or more requests'
WHEN 'Sleeping ' THEN 'Sleeping - Currently running no requests'
WHEN 'Preconnect ' THEN 'Session is in the Resource Governor classifier'
ELSE 'Dormant – Session is in prelogin state' END status
FROM sys.dm_exec_sessions
GROUP BY status
```

Just a quick count of all the transaction isolation levels

sql
SELECT COUNT(*),CASE transaction_isolation_level
WHEN 0 THEN 'Unspecified'
WHEN 1 THEN 'ReadUncomitted'
WHEN 2 THEN 'Readcomitted'
WHEN 3 THEN 'Repeatable'
WHEN 4 THEN 'Serializable'
WHEN 5 THEN 'Snapshot' END AS TRANSACTION_ISOLATION_LEVEL
FROM sys.dm_exec_sessions
GROUP BY transaction_isolation_level
```

To see what the SET options are that you are using in your connection, use the following query, leave out the WHERE clause if you want to know it for all connections. The query returns pretty much what DBCC USERINFO returns but you can run this for all connected sessions in one shot

sql
SELECT @@SPID AS SPID,
 text_size,
 language,
 lock_timeout,
 date_first,
 date_format,
CASE quoted_identifier
WHEN 1 THEN 'SET' ELSE 'OFF' END QUOTED_IDENTIFIER,
CASE arithabort
WHEN 1 THEN 'SET' ELSE 'OFF' END ARITHABORT,
CASE ansi_null_dflt_on
WHEN 1 THEN 'SET' ELSE 'OFF' END ANSI_NULL_DFLT_ON,
CASE ansi_defaults
WHEN 1 THEN 'SET' ELSE 'OFF' END ANSI_DEFAULTS ,
CASE ansi_warnings
WHEN 1 THEN 'SET' ELSE 'OFF' END ANSI_WARNINGS,
CASE ansi_padding
WHEN 1 THEN 'SET' ELSE 'OFF' END ANSI_PADDING,
CASE ansi_nulls
WHEN 1 THEN 'SET' ELSE 'OFF' END ANSI_NULLS,
CASE concat_null_yields_null
WHEN 1 THEN 'SET' ELSE 'OFF' END CONCAT_NULL_YIELDS_NULL,
CASE transaction_isolation_level
WHEN 0 THEN 'Unspecified'
WHEN 1 THEN 'ReadUncomitted'
WHEN 2 THEN 'Readcomitted'
WHEN 3 THEN 'Repeatable'
WHEN 4 THEN 'Serializable'
WHEN 5 THEN 'Snapshot' END AS TRANSACTION_ISOLATION_LEVEL
FROM sys.dm_exec_sessions
WHERE session_id = @@SPID
```

Here is another one where you had to run performance counter back in the 2000 days. This query will get you the page life expectancy for your server

sql
SELECT *
FROM sys.dm_os_performance_counters  
WHERE counter_name = 'Page life expectancy'
AND OBJECT_NAME = 'SQLServer:Buffer Manager'
```

Here is another short one, what account are my services running under?

sql
SELECT  distinct servicename,
 service_account,status_desc
FROM    sys.dm_server_services
```

Output

<pre>servicename			service_account			status_desc
SQL Server (MSSQLSERVER)	NT ServiceMSSQLSERVER		Running
SQL Server Agent (MSSQLSERVER)	NT ServiceSQLSERVERAGENT	Stopped</pre>

I only listed a handful of Dynamic Management Views, SQL Server 2008 R2 has 135 Dynamic Management Views, SQL Server 2012 as of CTP 3 has 174 Dynamic Management Views

Here is how you can get a list of all of them

sql
SELECT * FROM master.sys.sysobjects
WHERE name like 'dm[_]%'
```

The list below links to Books On Line for related Dynamic Management Views, if you want to know about mirroring Dynamic Management Views then click on the [Database Mirroring Related Dynamic Management Views][2] link. I would say, go to each section and maybe spend a week on it&#8230;by the middle of spring you should be the Dynamic Management Views master 🙂

[Change Data Capture Related Dynamic Management Views][3]
  
[I/O Related Dynamic Management Views and Functions][4]
  
[Change Tracking Related Dynamic Management Views][5]
  
[Object Related Dynamic Management Views and Functions][6]
  
[Common Language Runtime Related Dynamic Management Views][7]
  
[Query Notifications Related Dynamic Management Views][8]
  
[Database Mirroring Related Dynamic Management Views][2]
  
[Replication Related Dynamic Management Views][9]
  
[Database Related Dynamic Management Views][10]
  
[Resource Governor Dynamic Management Views][11]
  
[Execution Related Dynamic Management Views and Functions][12]
  
[Service Broker Related Dynamic Management Views][13]
  
[Extended Events Dynamic Management Views][14]
  
[SQL Server Operating System Related Dynamic Management Views][15]
  
[Full-Text Search Related Dynamic Management Views][16]
  
[Transaction Related Dynamic Management Views and Functions][17]
  
[Index Related Dynamic Management Views and Functions][18]
  
[Security Related Dynamic Management Views][19]
  
[Filestream-Related Dynamic Management Views (Transact-SQL)][20]

That is all for today, come back tomorrow for the next part in this series

 [1]: /index.php/DataMgmt/DataDesign/are-you-ready-for-sql
 [2]: http://msdn.microsoft.com/en-us/library/ms173571.aspx
 [3]: http://msdn.microsoft.com/en-us/library/bb522478.aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms190314.aspx
 [5]: http://msdn.microsoft.com/en-us/library/cc627419.aspx
 [6]: http://msdn.microsoft.com/en-us/library/bb630390.aspx
 [7]: http://msdn.microsoft.com/en-us/library/ms179982.aspx
 [8]: http://msdn.microsoft.com/en-us/library/ms187407.aspx
 [9]: http://msdn.microsoft.com/en-us/library/ms176053.aspx
 [10]: http://msdn.microsoft.com/en-us/library/ms181626.aspx
 [11]: http://msdn.microsoft.com/en-us/library/bb934218.aspx
 [12]: http://msdn.microsoft.com/en-us/library/ms188068.aspx
 [13]: http://msdn.microsoft.com/en-us/library/ms176110.aspx
 [14]: http://msdn.microsoft.com/en-us/library/bb677293.aspx
 [15]: http://msdn.microsoft.com/en-us/library/ms176083.aspx
 [16]: http://msdn.microsoft.com/en-us/library/ms174971.aspx
 [17]: http://msdn.microsoft.com/en-us/library/ms178621.aspx
 [18]: http://msdn.microsoft.com/en-us/library/ms187974.aspx
 [19]: http://msdn.microsoft.com/en-us/library/bb677257.aspx
 [20]: http://msdn.microsoft.com/en-us/library/ff773106.aspx