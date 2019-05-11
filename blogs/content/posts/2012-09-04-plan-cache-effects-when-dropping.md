---
title: Plan Cache effects when dropping columns
author: Ted Krueger (onpnt)
type: post
date: 2012-09-04T17:49:00+00:00
ID: 1712
excerpt: 'Recently the question was raised, "If you drop a column on a table, does it also drop the statistics and remove the cached plans that relates to the column?"  To answer the question on statistics directly, yes.  SQL Server will remove any statistics for&hellip;'
url: /index.php/datamgmt/dbadmin/plan-cache-effects-when-dropping/
views:
  - 20149
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Recently the question was raised, "If you drop a column on a table, does it also drop the statistics and remove the cached plans that relates to the column?"  To answer the question on statistics directly, yes.  SQL Server will remove any statistics for the column that is dropped on the table.  For the plan cache and any plans that relate back to either the statistics or the column, the answer isn't quite as easy.

<p style="padding-left: 30px;">
  1)      SQL Server will remove, in the plan that is cached, the reference to the statistics
</p>

<p style="padding-left: 30px;">
  2)      SQL Server will not remove the entire plan
</p>

This does raise a good question since we now know that SQL Server will not remove the entire plan from cache.  Essentially, that plan is not optimal any longer and would likely be removed or cycled out as the cache is used through the life between restarts or freeing the cache.  On larger SQL Server instances that are using a large amount of memory and cache, you could see some benefit from those plans being removed prior to the natural cycle.  Luckily, there is a way we can proactively remove them so we can immediately free those resources.

Let's look at one example and follow through with the entire process of how SQL Server reacts to a DROP COLUMN in regards to statistics and the plans cached.

Using the script in listing 1.1, create a table named, CustTable and insert some test data into it.

```sql
CREATE TABLE [dbo].[CustTable](
	[CustID] [int] IDENTITY(1,1) NOT NULL,
	[CustName] [varchar](150) NULL,
	[SalesQuotaID] [int] NULL,
	[BusRegionID] [int] NULL,
 CONSTRAINT [PK_CustTable] PRIMARY KEY CLUSTERED 
(
	[CustID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
INSERT INTO CustTable
SELECT 'Name',rand() * 100, rand() * 100
GO 70000
```

_Listing 1.1_

 

At this point, with AUTO\_STATS on, checking for existing statistics on the CustTable table will result in no statistics created (except for the primary key statistics, PK\_CustTable.  If we run a query such as

 

```sql
SELECT COUNT(*),CustName,BusRegionID 
FROM CustTable  
WHERE SalesQuotaID > 1
GROUP BY CustName,BusRegionID
```

_Listing 1.2_

 

We should see a result of three statistics created.  One for each custname, busregionid and salesquotaid.

```sql
SELECT * FROM sys.stats WHERE object_id = OBJECT_ID('dbo.CustTable')
```


_Listing 1.3_

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-158.png?mtime=1346787647"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-158.png?mtime=1346787647" width="624" height="86" /></a>
</div>

For SQL Server 2012, turn trace flag 8666 so we can examine the statistics being used by the plans in the cache.

```sql
DBCC TRACEON(8666)
GO
```

_Listing 1.4_

 __

Once trace flag 8666 is enabled, we can dig deep into dm\_exec\_cached\_plans and dm\_exec\_query\_plan while using XMLNAMESPACES to reference the field, wszStatName.  The plan from the query we ran earlier in listing 1.2 should be in the cache.  This can be verified by running the following query that uses the wszStatName Field Name to examine the statistics the plans referenced.

```sql
;WITH XMLNAMESPACES ('http://schemas.microsoft.com/sqlserver/2004/07/showplan' as showplan)
SELECT qt.text AS SQLCommand,
      qp.query_plan,
      Stat.ref.value('@FieldValue','NVarChar(500)') AS StatsName
FROM sys.dm_exec_cached_plans cp
CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle) qp
CROSS APPLY sys.dm_exec_sql_text (cp.plan_handle) qt
CROSS APPLY query_plan.nodes('//showplan:Field[@FieldName="wszStatName"]') Stat(ref)
WHERE qt.text LIKE '%CustTable%'
GO
```

_Listing 1.5_

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-159.png?mtime=1346787647"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-159.png?mtime=1346787647" width="624" height="61" /></a>
</div>

As we can see, the three statistics are shown in the results for the plan that was cached.

**Cleaning up after a DROP COLUMN**

Now that both statistics and a valid plan have been cached, drop the CustName column on the CustTable table.

```sql
ALTER TABLE CustTable
DROP COLUMN CustName
```

_Listing 1.6_

Rerun the query from listing 1.5.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-160.png?mtime=1346787648"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-160.png?mtime=1346787648" width="624" height="37" /></a>
</div>

Notice the statistic, \_WA\_Sys\_00000002\_02FC7413 is no longer referenced and shown in the results.  Overall, this plan is not optimal given the CustName column reference.  It would be ideal for SQL Server to remove it as part of the DROP COLUMN but given resource utilization and the mechanism for the plan being cycled out at the time of need, it isn't a critical situation.  However, as mentioned earlier, there can be value to clearing the plan from the cache for some instances.

**Removing plans from cache by column reference**

In order to remove a distinct plan from cache, FREEPROCCACHE can be used by passing in the plan\_handle as a parameter.  To determine all cached plans that reference a particular column, the DMVs dm\_exec\_query\_stats and dm\_exec\_query_plan with XMLNAMESPACES can be used in a manner similar to how we identified the statistics referenced by a specific plan.

```sql
;WITH XMLNAMESPACES ('http://schemas.microsoft.com/sqlserver/2004/07/showplan' as showplan)
SELECT cp.plan_handle
FROM sys.dm_exec_query_stats AS cp (NOLOCK)
	CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle) AS qp
	CROSS APPLY query_plan.nodes( '//showplan:ColumnReference' ) Cols(ref)
WHERE Cols.ref.value('@Column', 'SYSNAME') = 'CustName'
GROUP BY cp.plan_handle
GO
```

_Listing 1.7_

Listing 1.7 will return all plan handles that reference the column CustName.  We can take advantage of the ability to identify those plan handles and combine it with dynamic SQL to then  iterate through each plan handle returned in order to execute DBCC FREEPROCCACHE, which allows us to fully automate the removal of these plans.

```sql
;WITH XMLNAMESPACES ('http://schemas.microsoft.com/sqlserver/2004/07/showplan' as showplan)
SELECT cp.plan_handle
        ,ROW_NUMBER() OVER (ORDER BY cp.plan_handle) AS ROWID
INTO #RemovePlans
FROM sys.dm_exec_query_stats AS cp (NOLOCK)
	CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle) AS qp
	CROSS APPLY query_plan.nodes( '//showplan:ColumnReference' ) Cols(ref)
WHERE Cols.ref.value('@Column', 'SYSNAME') = 'CustName'
GROUP BY cp.plan_handle
 
DECLARE @LOOP INT = 1
DECLARE @PLAN_HANDLE VARBINARY(64)
DECLARE @FREECACHE VARCHAR(450)
 
WHILE (@LOOP <= (SELECT MAX(ROWID) FROM #RemovePlans))
 BEGIN
  SET @PLAN_HANDLE = (SELECT plan_handle FROM #RemovePlans WHERE ROWID = @LOOP)
  SET @FREECACHE = (SELECT 'DBCC FREEPROCCACHE (plan_handle=' + CONVERT(VARCHAR(91), @PLAN_HANDLE, 1) + ')')
  Exec(@FREECACHE)
  SET @LOOP += 1
 END
```

_Listing 1.8_

 

Using listing 1.7, examine the cache to see if the column is referenced in any remaining cached plans.  Zero results should be returned.

**Summary**

In many cases, the cache can be left to recycling plans as it sees fit.  However, in large cache intense needs, or in cases where large plans consume significant space in the cache, planning to clear specific plans as we have shown today can have value.  Taking into account a zero downtime SQL Server, this method can also prove valuable as opposed to some methods when administrators will restart SQL Server or run a FREEPROCCACHE to clear all the plans and allow the plans to compile back to cache.

Also, as a reality check, dropping columns doesn't happen daily or in some cases, yearly.  Given the needs for dropping columns is so rare, this is not a good automated process candidate.  These types of plan cache manipulation and topics should always be review with great scrutiny and manually executed based on the findings such as the ones we have shown here in this article.