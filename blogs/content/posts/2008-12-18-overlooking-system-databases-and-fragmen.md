---
title: Overlooking system databases and fragmentation
author: Ted Krueger (onpnt)
type: post
date: 2008-12-18T12:13:32+00:00
ID: 256
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/overlooking-system-databases-and-fragmen/
views:
  - 16085
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
A common mistake DBAs make is overlooking the need to maintain the indexes that ship with system databases. Fragmentation is a concern on user databases but also is something that must be maintained on databases like ReportServer for SSRS. 

I'm going to focus on SSRS as an example but keep this open to all of the systems databases you have on your instances. 

Most successful reporting services implementations come with daily report generations, changes and high execution rates. That means the tables in the ReportServer database will change along with your reports. One key table to focus on for maintaining performance will be the ExecutionLog. One reporting services instance I have goes well over the 75% fragmentation mark around every 6 hours. That will be common for the ExecutionLog and in larger companies you can see indexes becoming fragmented in as little as the hour increments.

The ExecutionLog table in ReportServer has one clustered index by default, “IX_ExecutionLog”

With that we can join to [SQLDenis's][1] blog on fragmentation by running 

```sql
Use ReportServer
Go
SELECT OBJECT_NAME(OBJECT_ID) AS Tablename,s.name AS Indexname
,index_type_desc
,avg_fragmentation_in_percent
,page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') d
join sysindexes s ON d.OBJECT_ID = s.id
and d.index_id = s.indid
and s.name ='IX_ExecutionLog'
```
The results from this yield 83% on the reporting services instance I ran it on. Given that we need to REBUILD the index as the guidelines typically tell us this is beyond REORGANIZE 

```sql
ALTER INDEX IX_ExecutionLog ON ExecutionLog REBUILD 
	WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = ON, 
	ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON, 
	SORT_IN_TEMPDB = OFF, ONLINE = OFF)
```
Now running the query again our fragmentation has gone down to 0. Note that rebuilding large tables will lock the tables so if you are on Enterprise edition then use the ONLINE option or plan the best time to run the REBUILD so there is no interruption to your user community.

In general any table that becomes highly fragmented will cause performance problems so there will be a need for maintaining that fragmentation not only on the user databases but the systems databases you implement. My recommendation is to setup SQL Agent jobs to monitor the fragmentation on databases like ReportServer for a week. Determine the fragmentation level you are at within a time frame. This will allow you to determine a job to maintain fragmentation with minimal efforts from you.

 [1]: /index.php/DataMgmt/DataDesign/finding-fragmentation-of-an-index-and-fi