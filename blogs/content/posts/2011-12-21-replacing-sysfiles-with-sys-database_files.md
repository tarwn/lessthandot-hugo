---
title: Replacing sysfiles With sys.database_files
author: Jes Borland
type: post
date: 2011-12-21T11:42:00+00:00
ID: 1455
excerpt: "The SQL 2000 virtual table sysfiles, and the corresponding SQL 2005 + compatibility view sys.sysfiles, will be removed in a future version of SQL Server. What's the replacement?"
url: /index.php/datamgmt/dbprogramming/replacing-sysfiles-with-sys-database_files/
views:
  - 10642
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I was recently working on a new presentation about filegroups (debuting in January! Come see me at the [Wisconsin SSUG][1] on January 10, 2012.). I was looking for a query to tie filegroups and files together. I found several blogs about it, but all of them referenced sysfiles. 

Why is this a problem? The SQL 2000 virtual table sysfiles, and the corresponding SQL 2005 + compatibility view sys.sysfiles, will be removed in a future version of SQL Server. Our very own Denis Gobo has been [writing a series][2] about updating your code to use new syntax. 

In that case, what's the new hotness? 

We should now be using the catalog view sys.database_files. Here, I'll show you this view on SQL 2008R2, using AdventureWorks2008R2. 

Here are the columns returned when querying sysfiles. 

```sql
USE AdventureWorks2008R2;
GO

SELECT fileid, groupid, size, maxsize, growth, status, perf, name, filename
FROM sysfiles;
GO 
```
<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/sysfiles all columns.JPG?mtime=1324351775"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/sysfiles all columns.JPG?mtime=1324351775" width="805" height="99" /></a>
</div>

Moving on, here is the query of sys.sysfiles. 

```sql
USE AdventureWorks2008R2;
GO

SELECT fileid, groupid, size, maxsize, growth, status, perf, name, filename
FROM sys.sysfiles;
```
<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/sys.sysfiles all columns.JPG?mtime=1324351775"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/sys.sysfiles all columns.JPG?mtime=1324351775" width="813" height="95" /></a>
</div>

This query's result is the same as the original virtual table. 

Now, the new hotness, sys.database_files. 

```sql
USE AdventureWorks2008R2;
GO

SELECT *
FROM sys.database_files;
```
<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/sys.database_files all columns.JPG?mtime=1324351775"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/sys.database_files all columns.JPG?mtime=1324351775" width="1093" height="118" /></a>
</div>

There's an immediate difference here in the number of columns returned. The new view is much more comprehensive than the old table. 

What's new? 

sys.database_files includes more than just the data and log files. It will also show FILESTREAM and fulltext files. 

The state_desc can tell you if the file is ONLINE, OFFLINE, RESTORING, and so forth.

There are fields to tell you if the file is read-only, if it's on read-only media, and if it's a sparse file. 

Let's look at a couple of useful queries using this new view. 

A query to determine file size (with a little help from Bob Pusateri ([twitter][3] | [blog][4])) is: 

```sql
SELECT fg.data_space_id AS FGID,
   (f.file_id) AS FileCount,
   ROUND(CAST((f.size) AS FLOAT)/128,2) AS Reserved_MB,
   ROUND(CAST((FILEPROPERTY(f.name,'SpaceUsed')) AS FLOAT)/128,2) AS Used_MB,
   ROUND((CAST((f.size) AS FLOAT)/128)-(CAST((FILEPROPERTY(f.name,'SpaceUsed'))AS FLOAT)/128),2) AS Free_MB
FROM sys.filegroups fg
	LEFT JOIN sys.database_files f ON f.data_space_id = fg.data_space_id
```

Another useful query, especially as it related to what I was doing, is to determine what filegroup a file is on. 

```sql
SELECT FG.name as FilegroupName, F.file_id, F.name as [FileName] 
FROM sys.database_files F 
	INNER JOIN sys.filegroups FG ON FG.data_space_id = F.data_space_id;
```

As Microsoft moves away from the old virtual tables and compatibility views, make sure your T-SQL is also up to date. It's a good idea to review scripts that have been in use for some time, and see if they can be updated.

 [1]: http://wisconsin.sqlpass.org/
 [2]: /index.php/DataMgmt/DataDesign/are-you-ready-for-sql
 [3]: http://twitter.com/sqlbob
 [4]: http://www.bobpusateri.com