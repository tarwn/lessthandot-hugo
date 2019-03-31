---
title: Shrink log in all your user databases
author: Naomi Nosonovsky
type: post
date: 2009-07-12T01:40:34+00:00
url: /index.php/datamgmt/datadesign/truncate-log-in-all-your-user-databases/
views:
  - 38812
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
A recent post in the forum I frequent presented an interesting problem &#8211; shrink log file in all user databases. See the reasons why it may not be a good idea explained in [Do not truncate your LDF files][1] and [Why you want to be restrictive with shrink of database files][2]. 

The first idea that came to mind was to use sp_MSForEachDB non-documented stored procedure for this task. You can find this [article by Arshad Ali][3] very helpful in understanding sp\_MsForEachTable and sp\_MSForEachDB stored procedures and their parameters.
  
<!--more-->


  
<span class="MT_red">UPDATE</span>
  
Based on Duncan&#8217;s comments the better code than I suggested would be

<pre>sp_MSForEachDb 'IF ''?'' NOT IN (''master'', ''tempdb'', ''tempdev'', ''model'', ''msdb'')
AND (SELECT recovery_model FROM master.sys.databases WHERE name = ''?'') = 1
AND (SELECT is_read_only FROM master.sys.databases WHERE name = ''?'') = 0
BEGIN
declare @LogFile nvarchar(2000)
USE [?]
SELECT @LogFile = sys.database_files.name
FROM sys.database_files
WHERE (sys.database_files.type = 1)
PRINT @LogFile
EXEC(''ALTER DATABASE [?] SET RECOVERY SIMPLE'')
DBCC SHRINKFILE (@LogFile, 1)
EXEC(''ALTER DATABASE [?] SET RECOVERY FULL'')
END'</pre>

It may be even a better idea of using dynamic SQL and implicit looping through databases instead of undocumented SP, like this

<pre>declare @SQL nvarchar(max)

  select @SQL = coalesce(@SQL + char(13) + char(10),'') + N'
 Use ' + QUOTENAME(d.[name]) + ';' + CHAR(13) + '
 ALTER DATABASE ' + QUOTENAME(d.[name]) + ' SET RECOVERY SIMPLE;
 DBCC SHRINKFILE (' + quotename(mf.[name],'''') + ', 1);
 ALTER DATABASE ' + QUOTENAME(d.[name]) + ' SET RECOVERY FULL;'
FROM sys.databases d
INNER JOIN sys.master_files mf ON [d].[database_id] = [mf].[database_id]
WHERE
    d.[database_id] &gt; 4 --no sys dbs
    AND d.recovery_model = 1
    AND d.is_read_only = 0
    AND mf.[type] = 1 --log files
ORDER BY d.name

--print @SQL

execute (@SQL)</pre>

(code based on this [MSDN thread][4])
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
Interestingly, this solution, proposed by Borislav Borissov, still attempts to change recovery model in TempDB &#8211; not clear why:

<pre>sp_MSForEachDb 'IF LOWER(''?'') NOT IN (''master'', ''tempdb'', ''tempdev'', ''model'', ''msdb'')
                 BEGIN
                     declare @LogFile nvarchar(2000)
                     declare @ExeString nvarchar(2000)
                     USE [?]
                     SELECT @LogFile = sys.sysaltfiles.name
                            FROM sys.sysdatabases
                     INNER JOIN sys.sysaltfiles ON sys.sysdatabases.dbid = sys.sysaltfiles.dbid
                     WHERE (sys.sysaltfiles.fileid = 1) AND (sys.sysdatabases.name = ''?'')
                     print @LogFile
                     ALTER DATABASE [?] SET RECOVERY SIMPLE
                     DBCC SHRINKFILE (@LogFile, 1)
                     ALTER DATABASE [?] SET RECOVERY FULL
                 END'</pre>

However, the solution suggested by George Mastros of first creating a stored procedure and then executing it, works fine

<pre>sp_msforeachdb 'If ''?'' Not In (''master'',''tempdb'',''model'',''msdb'') 
      Begin
            Declare @LogFile nvarchar(2000)
            Select @LogFile = Name From master..sysaltfiles Where db_name(dbid) = ''?'' And Fileid = 2
            Set @LogFile = ''Create Procedure dbo.ShrinkMe As 
                  ALTER DATABASE [?] SET RECOVERY SIMPLE
                  dbcc ShrinkFile(['' + @LogFile + ''])
                  ALTER DATABASE [?] SET RECOVERY FULL''
 
            use [?]
            If Exists(Select * From [?].Information_Schema.Routines Where Specific_Name = ''ShrinkMe'')
                  Begin
                        drop Procedure ShrinkMe
                  End
            use [?]     
            Exec (@LogFile)   
            --print(@LogFile)
            exec [?].dbo.ShrinkMe
      End '</pre>

This link allows to check the recovery model for the database
   
http://blog.sqlauthority.com/2009/07/16/sql-server-four-different-ways-to-find-recovery-model-for-database/

\*** **Remember, if you have a SQL related question, try our [Microsoft SQL Server Programming][5] forum or our [Microsoft SQL Server Admin][6] forum**<ins></ins>

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/do-not-truncate-your-ldf-files
 [2]: http://www.karaszi.com/SQLServer/info_dont_shrink.asp
 [3]: http://www.mssqltips.com/tip.asp?tip=1905&ctc
 [4]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/414973eb-a84b-40c4-8a21-919d92947ed0/#4c9c3420-495c-4e0f-ad94-6fbb4b7c44fb
 [5]: http://forum.ltd.local/viewforum.php?f=17
 [6]: http://forum.ltd.local/viewforum.php?f=22