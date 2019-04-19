---
title: 'T-SQL Tuesday #25: T-SQL Tricks – Checking Transaction Log Space Used'
author: Jes Borland
type: post
date: 2011-12-13T11:15:00+00:00
ID: 1439
excerpt: "The twenty-fifth installation of T-SQL Tuesday is brought to us by Allen White. This month's topic is “T-SQL Tricks”."
url: /index.php/datamgmt/dbadmin/t-sql-tuesday-25-t/
views:
  - 8820
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
[![][1]][2]
  
The twenty-fifth installation of T-SQL Tuesday is brought to us by Allen White ([twitter][3] | [blog][4]). The topic is [“T-SQL Tricks”][2]. 

As a database administrator, one of the most common calls I receive from developers is, “I'm getting an error saying the log is full.” This could happen for a variety of reasons:

• Data was being loaded into the database.
  
• An index was being built, rebuilt, or reorganized.
  
• A DBCC CHECKDB was running.
  
• There is an open transaction. 

I could have simply expanded the transaction log file without any questions, but, if you know me, you know I like to question things. 

What I frequently wanted to know was how large the log was, how much space was being used, and what recovery model the database was in. I put together a simple script to check this information, and used it frequently. 

I'm able to pull information using a DBCC command, SQLPERF, and the system catalog view, sys.databases. Having this information exposed is very helpful to us. 

```sql
-- Set database name 
DECLARE @DatabaseName VARCHAR(50);
SET @DatabaseName = 'AdventureWorks2008R2'; 

-- Get database ID. This is helpful to know in the next step, especially if you have an instance with hundreds of databases. 
SELECT db_id(@DatabaseName);

-- Check log size and space used. 
DBCC SQLPERF(logspace);

-- Check recovery mode and why log space can't be reused. 
SELECT name, recovery_model_desc, log_reuse_wait_desc
FROM sys.databases
WHERE name = @DatabaseName;
```

DBCC SQLPERF is used to get transaction log usage information. It returns Database Name, Log Size (MB), Log Space Used (%), and Status. It requires VIEW SERVER STATE permissions. In addition, you can reset DMV information for latch statistics or wait statistics by passing in parameters. This can help with additional troubleshooting. Here's the full syntax: 

```sql
DBCC SQLPERF 
(
     [ LOGSPACE ]
     |
     [ "sys.dm_os_latch_stats" , CLEAR ]
     |
     [ "sys.dm_os_wait_stats" , CLEAR ]
) 
     [WITH NO_INFOMSGS ]
```

sys.databases will provide information about each database on a SQL Server instance. Just a few of the things you can view are the compatibility level, collation, recovery model, and state. Here, I'm returning the database name, the recovery model, and what the log is waiting on. If a user can connect to a database, they can view the sys.databases information for that database. 

A great reference (and one of my inspirations for this script) was Gail Shaw's blog, [“Why is my transaction log full?”][5] With this information, I could choose to change the recovery model, expand the log, or wait for the next transaction log backup. 

If the database was in full recovery mode, and the users did not need point-in-time recovery (perhaps it's a development environment), I could choose to set it to simple recovery. If there would be valid reasons for the expansion – frequent data loads, or long-running queries – I could expand the log to a reasonable size. If the database was in full recovery, and this was a one-time operation, I could issue a transaction log backup to clear space. Knowing why a transaction log is expanding, and if it will happen again, is an important part of your job as a DBA. 

Advice I would give to anyone new to SQL Server, and even those who have worked with it for some time, is to become familiar with the system catalog views. Catalog views expose the catalog metadata that the SQL Server Engine uses. They are well documented in BOL and efficient for retrieving valuable information to make logical decisions. Catalog views are a powerful way to troubleshoot SQL Server.

 [1]: /wp-content/uploads/blogs/DataMgmt/olap_1.gif ""
 [2]: http://sqlblog.com/blogs/allen_white/archive/2011/12/05/t-sql-tuesday-025-invitation-to-share-your-tricks.aspx
 [3]: https://twitter.com/#!/SQLRunr
 [4]: http://sqlblog.com/blogs/allen_white/default.aspx
 [5]: http://www.sqlservercentral.com/articles/Transaction+Log/72488/