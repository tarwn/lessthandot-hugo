---
title: Find the last Backup taken in SQL Server
author: Christiaan Baes (chrissie1)
type: post
date: 2008-05-17T05:55:54+00:00
ID: 27
url: /index.php/datamgmt/dbprogramming/mssqlserver/find-the-last-backup-taken-in-sql-server/
views:
  - 3252
categories:
  - Microsoft SQL Server

---
```
SELECT *
FROM msdb.dbo.backupmediafamily backupmediafamily
JOIN msdb.dbo.backupset backupset ON backupmediafamily.media_set_id = backupset.media_set_id
and backupset.backup_start_date = (SELECT max(backup_start_date)
FROM msdb.dbo.backupset child
WHERE child.database_name = backupset.database_name and child.type = 'D')
and database_name = 'ReplaceWithDatabaseNameHere'
and backupset.type = 'D'```
This is based on something I found here. Thank you mrdenny.

And this works in SQL Server 2000.