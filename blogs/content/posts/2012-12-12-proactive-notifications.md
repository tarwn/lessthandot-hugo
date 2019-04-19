---
title: 'SQL Advent 2012 Day 12: Proactive notifications'
author: SQLDenis
type: post
date: 2012-12-12T08:42:00+00:00
ID: 1843
excerpt: This is day twelve of the SQL Advent 2012 series of blog posts. Today we are going to look at SQL Server proactive notifications
url: /index.php/datamgmt/dbprogramming/proactive-notifications/
views:
  - 7565
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - errors
  - notifications
  - sql advent 2012
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012
  - ssms

---
This is day twelve of the [SQL Advent 2012 series][1] of blog posts. Today we are going to look at SQL Server proactive notifications. In the [SQL Server Maintenance][2] post from yesterday I touched upon proactive notifications a little, today I want to dive a little deeper into this subject. 

The last thing you want as a DBA is to hear any of the following things from the end users

  * The transaction log is full
  * The database is very slow
  * The latest backup we have is 9 days old
  * The table that was created has 2 extra columns this morning
  * Everything is locked up can't get any results back from a query
  * Deadlocks are occurring

What you really want to have at your shop is a tool like [Quest Foglight][3], [Confio Ignite][4], [Red Gate SQL Monitor][5] or similar. The benefit of these tools is that there is a central location where you can look at all the alerts at a glance. You get a lot of stuff out of the box and all you have to do is tell it what server to start monitoring. I would suggest to start using the trial version to see if it is something that would be beneficial for your organization.

Of course you can roll your own solution as well, this will involve work and unless your time is worthless or you are bored out of your mind after work I wouldn't do it.

## Utilize the logs

You need to scan the errorlog periodically to see if there are errors, you can automate this, no need to start opening log files every 5 minutes. create a SQL Agent job that runs every 5 minutes and checks if there are any errors since it last ran. You can use the xp_readerrorlog proc to read the error log from with sql server with T-SQL.

Here is a small example of what you can do if you have this in a SQL Agent job that runs every 5 minutes or so, you can of course email yourself the results, dump the result into a table that is perhaps shown on a dashboard in the office, there are many possibilities.

```sql
--This will hold the rows
CREATE TABLE #ErrorLog (LogDate datetime, ProcessInfo VarChar(10), ErrorMessage VarChar(Max))

-- Dump the errorlog into the table
INSERT INTO #ErrorLog
EXEC master.dbo.xp_readerrorlog

-- Delete everything older than 5 minutes
-- ideally you will store the max date when it ran last
DELETE #ErrorLog
WHERE LogDate <  DATEADD(mi,-5,GETDATE())

-- Some stuff you want to check for
-- Failed backups...you want to know this
SELECT * FROM #ErrorLog
WHERE ErrorMessage LIKE'BACKUP failed%'

-- Why does it take so looong to grow a file, maybe rethink your settings
SELECT * FROM #ErrorLog
WHERE ErrorMessage LIKE'Autogrow of file%'

-- What is going on any backups or statistic updates running at this time?
SELECT * FROM #ErrorLog
WHERE ErrorMessage LIKE'SQL Server has encountered %occurrence(s) of I/O requests taking longer than%'

-- My mirror might not be up to date
SELECT * FROM #ErrorLog
WHERE ErrorMessage LIKE'The alert for ''unsent log'' has been raised%'


DROP TABLE #ErrorLog 
```
Those are just small samples, you might want to look for other kind of messages from the errorlog

## The transaction log is full

You want to make sure that you know you are running out of space before you run out of space. I covered this in the [SQL Server Maintenance][2] post Take a look at the sections _Make sure that you have enough space left on the drives_ and _Make sure that you have enough space left for the filegroups_ In those two section I described what to look for and also supplied code that you can then plug into your own solution

## The database is very slow

This complaint you hear every now and then, I have seen this from time to time. There are several things that could be happening, here is a list

Someone decided to take a backup of that 1 TB database in the middle of the day
  
The update statistics job is still running
  
Statistics are stale and haven't been updated in a long time
  
The virus scan is running amok and nobody told it to ignore the database files
  
Someone decided to query all the data all at once

If you have a tool like [Quest Foglight][3], [Confio Ignite][4], [Red Gate SQL Monitor][5] or similar then you can see what query ran at what time, what it did and how long it ran.

You can of course also use sp_who2, BlkBy column and DBCC INPUTBUFFER to see what is going on

If you like to use Dynamic Management Views, then take a look at Glenn Berry's [SQL Server 2005 Diagnostic Information Queries (Dec 2012)][6] and [SQL Server 2008 Diagnostic Information Queries (Nov 2012)][7] posts, there is a .sql file in each post with all kind of queries to discover all kinds of stuff about your server. 

It could also be that your hardware is having issues, make sure the IOs look good and check the eventlog for any clues.

## The latest backup we have is 9 days old

The following query will give you for all the databases the last time it was backed up or display NEVER if it wasn't backed up

```sql
SELECT s.Name AS DatabaseName,'Database backup was taken on  ' + 
CASE WHEN MAX(b.backup_finish_date) IS NULL THEN 'NEVER!!!' ELSE
CONVERT(VARCHAR(12), (MAX(b.backup_finish_date)), 101) END AS LastBackUpTime
FROM sys.sysdatabases s
LEFT OUTER JOIN msdb.dbo.backupset b ON b.database_name = s.name
GROUP BY s.Name
```

Here is what the output will look like

<pre>DatabaseName	LastBackUpTime
--------------  ---------------------------------------
model	        Database backup was taken on  NEVER!!!
msdb	        Database backup was taken on  12/10/2012
ReportServer	Database backup was taken on  NEVER!!!</pre>

As you can see that is not that great, all the databases should be backed up on a regular basis. Scroll up to the _Utilize the logs_ section to see how you can check the errorlog for failed backup messages.

## Everything is locked up, you can't get any results back from a query

Usually this indicates that there is an open transaction somewhere that has not finished or someone did the BEGIN TRAN part but never did a COMMIT or ROLLBACK.

Some people just restart the server to 'fix' the issue, of course if you do that you will never know what the root cause is and you never know when it will happen again.

We can easily show what happens when you have an open transaction, btw don't do this on the production server.

In 1 query window run this, replace SomeTable with a real table name.

```sql
BEGIN TRAN

SELECT TOP 1 * FROM SomeTable WITH(UPDLOCK, HOLDLOCK)
```

You will get a message that the query completed successfully

In another window run this

```sql
SELECT TOP 1 * FROM SomeTable WITH(UPDLOCK, HOLDLOCK)
```

That query won't return anything unless the first one is commited or rolled back
  
Now run this query below, the first column should have the text AWAITING COMMAND

```sql
SELECT   sys.cmd
        ,sys.last_batch
        ,lok.resource_type
        ,lok.resource_subtype
        ,DB_NAME(lok.resource_database_id)
        ,lok.resource_description
        ,lok.resource_associated_entity_id
        ,lok.resource_lock_partition
        ,lok.request_mode
        ,lok.request_type
        ,lok.request_status
        ,lok.request_owner_type
        ,lok.request_owner_id
        ,lok.lock_owner_address
        ,wat.waiting_task_address
        ,wat.session_id
        ,wat.exec_context_id
        ,wat.wait_duration_ms
        ,wat.wait_type
        ,wat.resource_address
        ,wat.blocking_task_address
        ,wat.blocking_session_id
        ,wat.blocking_exec_context_id
        ,wat.resource_description
FROM    sys.dm_tran_locks lok
JOIN    sys.dm_os_waiting_tasks wat
ON      lok.lock_owner_address = wat.resource_address
JOIN	sys.sysprocesses sys ON wat.blocking_session_id = sys.spid
```

As you can see you have a blocking\_session\_id and a session\_id, this will tell you which session\_id is being blocked. You can now verify that the transaction session_id is blocking the other id

Go back to that first command window and execute a rollback

```sql
ROLLBACK
```

The query that had that second select should now be done as well, if you run that query that checks for the waits it should be clean as well.

Of course you could have done the same excercise by running sp\_who2, looking at the BlkBy column, finding out what that session is doing by running DBCC INPUTBUFFER(session\_id) with that session_id

## Deadlocks are occurring

There is already a post written on LessThanDot explaining how you can get emailed when deadlocks occur. Ted Krueger wrote that post and it can be found here: [Proactive Deadlock Notifications][8]

## Summary

I only touched the surface of what can be done in this post. I want you to find out if there is any monitoring being done in your shop, who gets notified? I have worked in places where the end user was the proactive notification, as long as we fixed it before the business users started to complaint life was good. Manual notifications and homebrew solutions might work for a while but when you add more and more servers and you add more people to the team this becomes laborious and error prone.

If there is one New Year's resolution you should add next year, I would suggest proactive notifications. Get trial versions of the tools, try them out until January 1st and then decide if you think it is worth.

That is all for day twelve of the [SQL Advent 2012 series][1], come back tomorrow for the next one, you can also check out all the posts from last year here: [SQL Advent 2011 Recap][9]

 [1]: /index.php/DataMgmt/DBProgramming/sql-advent-2012-here-is
 [2]: /index.php/DataMgmt/business-intelligence-1/sql-server-maintenance
 [3]: http://www.quest.com/foglight-for-sql-server/
 [4]: http://www.confio.com/performance/sql-server/ignite/
 [5]: http://www.red-gate.com/products/dba/sql-monitor/
 [6]: http://sqlserverperformance.wordpress.com/2012/12/06/sql-server-2005-diagnostic-information-queries-dec-2012/
 [7]: http://sqlserverperformance.wordpress.com/2012/11/27/sql-server-2008-diagnostic-information-queries-nov-2012/
 [8]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/proactive-deadlock-notifications
 [9]: /index.php/DataMgmt/DataDesign/sql-advent-2011-recap