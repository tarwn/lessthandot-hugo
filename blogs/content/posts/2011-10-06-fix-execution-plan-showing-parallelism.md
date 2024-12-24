---
title: Fix Execution Plan showing Parallelism
author: Ted Krueger (onpnt)
type: post
date: 2011-10-06T16:11:00+00:00
ID: 1342
excerpt: |
  "I see a ton of parallelism in my execution plan I'm tuning.  I read something online and I'm going to alter the value in Maximum Degree of Parallelism (MAXDOP) so they go away."
  This is a very common problem and a very common solution that is found on&hellip;
url: /index.php/datamgmt/dbprogramming/fix-execution-plan-showing-parallelism/
views:
  - 12627
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
"_I see a ton of parallelism in my execution plan I'm tuning.  I read something online and I'm going to alter the value in Maximum Degree of Parallelism (MAXDOP) so they go away."_

This is a very common problem and a very common solution that is found on the great wide internet of SQL and Google search results.  The first thing is, don't blanket change MAXDOP simply because you read it somewhere or someone says they did it or always do it.  This isn't a good _default_ thing to set typically.  Some specific database servers can take this type of change but it is more of a diagnosed and specific change. 

The question in the beginning of this blog is fictitious but let's pretend it is real and play it out.

Load up some data into a table.  Remember, Parallelism may only show on a table larger than normal, so load a good deal of data into the table.

```sql
CREATE TABLE LotsOhColumns 
(
	id int identity(1,1) primary key
	,fk_id uniqueidentifier 
	,col1 varchar(20)
	,col2 varchar(20)
	,col3 varchar(20)
	,col4 varchar(20)
	,col5 varchar(20)
	,col6 varchar(20)
	,col7 varchar(20)
	,col8 varchar(20)
	,col9 varchar(20)
	,col10 varchar(20)
	,col11 varchar(20)
	,col12 varchar(20)
)
GO

INSERT INTO LotsOhColumns 
SELECT NEWID(),'col1','col2','col3','col4','col5','col6','col7','col8','col9','col10','col11','col12'
GO 3000000
```
Now let's run a basic query on the table

```sql
SELECT [fk_id]
      ,[col1]
      ,[col2]
      ,[col3]
      ,[col4]
      ,[col5]
  FROM [XMLContent].[dbo].[LotsOhColumns]
WHERE fk_id = NEWID() 
```<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-28.png?mtime=1317924562"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-28.png?mtime=1317924562" width="689" height="95" /></a>
</div>

Looking at this plan, tons of issues come up.  The first one that we insist on tuning is the parallelism and run out to change MAXDOP.  Not yet everyone!

 

Looking at the query we need a nonclustered index on fk_id and include on the resulting columns.  This should prove to be useful given the results and predicate.

```sql
CREATE INDEX IDX_COVERING_ASC ON LotsOhColumns 
(
   [fk_id]
)
INCLUDE 
(
   [col1]
  ,[col2]
  ,[col3]
  ,[col4]
  ,[col5]
) 
```
Executing the query again shows a much cleaner plan

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-29.png?mtime=1317924562"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/All/-29.png?mtime=1317924562" width="376" height="92" /></a>
</div></p> 

**What did we learn here?**

Don't believe everything you read or are told on the internet.  Even this blog should be verified by official documentation and highly skilled experts in the field.  The results shown here is the MAXDOP setting should not be an easy setting to run to when parallelism is shown in execution plans.  Tune the plan and when that is accomplished and if parallelism is truly an issue on your highly active OLTP SQL Server installations, then take a look at MAXDOP.

Two resources that will assist on this topic greatly are the new book [Troubleshooting SQL Server: A Guide for the Accidental DBA][1], specifically the chapter on CPU and SQL Server.  Also, highly recommend fellow SQL Server MVP Paul White's paper, "[Understanding and Using Parallelism in SQL Server][2]" to read more on effects and why, how SQL Server uses Parallelism. 

 [1]: http://www.simple-talk.com/books/sql-books/troubleshooting-sql-server-a-guide-for-the-accidental-dba/
 [2]: http://www.simple-talk.com/sql/learn-sql-server/understanding-and-using-parallelism-in-sql-server/