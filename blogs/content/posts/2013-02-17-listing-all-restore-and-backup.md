---
title: Listing all RESTORE and BACKUP operations currently going on your SQL Server
author: SQLDenis
type: post
date: 2013-02-17T15:30:00+00:00
ID: 2003
excerpt: 'Sometimes you want to quickly see if there are any databases or logs being backed up or restored at this moment. I blogged at one point how you can check how much longer the restore will take here: How much longer will the SQL Server database restore ta&hellip;'
url: /index.php/datamgmt/dbprogramming/listing-all-restore-and-backup/
views:
  - 3856
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - backup
  - restore
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
Sometimes you want to quickly see if there are any databases or logs being backed up or restored at this moment. I blogged at one point how you can check how much longer the restore will take here: [How much longer will the SQL Server database restore take][1]. The other day someone wanted to know this information for all databases on a server, he wanted to know this for restores as well as backups. The query below will give you that info as well as the percentage that is complete for each operation

sql
SELECT 
    d.PERCENT_COMPLETE AS [%Complete],
    d.TOTAL_ELAPSED_TIME/60000 AS ElapsedTimeMin,
    d.ESTIMATED_COMPLETION_TIME/60000   AS TimeRemainingMin,
    d.TOTAL_ELAPSED_TIME*0.00000024 AS ElapsedTimeHours,
    d.ESTIMATED_COMPLETION_TIME*0.00000024  AS TimeRemainingHours,
    d.COMMAND as Command,
	s.text as CommandExecuted
FROM    sys.dm_exec_requests d
CROSS APPLY sys.dm_exec_sql_text(d.sql_handle)as s
WHERE  d.COMMAND LIKE 'RESTORE DATABASE%'
or d.COMMAND	 LIKE 'RESTORE LOG%'
OR d.COMMAND	 LIKE 'BACKUP DATABASE%'
OR d.COMMAND	 LIKE 'BACKUP LOG%'
ORDER   BY 2 desc, 3 DESC
```

Throw this in a view on your _Tools_ database and you are all set.

This will probably also be added to SQLCop's informational section

 [1]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/how-much-longer-will-the