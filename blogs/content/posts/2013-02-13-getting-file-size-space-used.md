---
title: Getting file size, space used and space free information for a SQL Server database
author: SQLDenis
type: post
date: 2013-02-13T18:14:00+00:00
ID: 1992
excerpt: 'Sometimes you want to quickly see how many files a database has, how much space a file is using and how much space is free. You can use the sysfiles/sys.files views or compatible views for that. From SQL Server 2005 onward you can also use the sys.datab&hellip;'
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/getting-file-size-space-used/
views:
  - 13990
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin
tags:
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - sql server 2012
  - sqlcop
  - tables

---
Sometimes you want to quickly see how many files a database has, how much space a file is using and how much space is free. You can use the sysfiles/sys.files views or compatible views for that. From SQL Server 2005 onward you can also use the sys.database_files catalog view. 

The sizes in these files are stored in 8k pages, in order to get the size in MB you need to divide by 128. If something is 131072 8K pages and you divide it by 128 you will get 1024

sql
SELECT 131072/128.0
```

That query gives you 1024

For SQL Server 2000 and up you can use this query

sql
SELECT
	a.FILEID AS file_id,
	CONVERT(DECIMAL(12,2),ROUND(a.size/128.000,2)) AS [file_size_mb],
	CONVERT(DECIMAL(12,2),ROUND(FILEPROPERTY(a.name,'SpaceUsed')/128.000,2)) AS [space_used_mb] ,
	CONVERT(DECIMAL(12,2),ROUND((a.size-FILEPROPERTY(a.name,'SpaceUsed'))/128.000,2)) AS [free_space_mb],
	a.NAME AS name,
	a.FILENAME AS physical_name
FROM
	sysfiles a
```

For SQL Server 2005 and up you can use the following query

sql
SELECT
	a.FILEID AS file_id,
	CONVERT(DECIMAL(12,2),ROUND(a.size/128.000,2)) AS [file_size_mb],
	CONVERT(DECIMAL(12,2),ROUND(FILEPROPERTY(a.name,'SpaceUsed')/128.000,2)) AS [space_used_mb] ,
	CONVERT(DECIMAL(12,2),ROUND((a.size-FILEPROPERTY(a.name,'SpaceUsed'))/128.000,2)) AS [free_space_mb],
	a.NAME AS name,
	a.FILENAME AS physical_name
FROM
	sys.sysfiles a
```

The only difference between the two queries is that the top query uses the system table sysfiles while the query below it uses the sys.sysfiles compatibility view

You can also use the sys.database_files catalog view from SQL Server 2005 onward. Here is what the query looks like, it is mostly the same except for some column names

sql
SELECT
	a.file_id ,
	CONVERT(DECIMAL(12,2),ROUND(a.size/128.000,2)) AS [file_size_mb],
	CONVERT(DECIMAL(12,2),ROUND(FILEPROPERTY(a.name,'SpaceUsed')/128.000,2)) AS [space_used_mb] ,
	CONVERT(DECIMAL(12,2),ROUND((a.size-FILEPROPERTY(a.name,'SpaceUsed'))/128.000,2)) AS [free_space_mb],
	name,
	a.physical_name
FROM
 sys.database_files a
```

This post will be added to the informational section of SQLCop