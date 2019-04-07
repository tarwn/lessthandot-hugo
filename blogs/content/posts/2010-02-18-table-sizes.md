---
title: Table sizes
author: niikola
type: post
date: 2010-02-18T15:21:52+00:00
excerpt: |
  I do not know if there is already code for table sizes (as used in SQL Server 2008 reports) but what I've seen on the net people are using cursors and  temporary tables (completely unnecessary). 
  
  NOTE (added later): 
  In order to update table sizes i&hellip;
url: /index.php/datamgmt/dbadmin/table-sizes/
views:
  - 6694
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I do not know if there is already code for table sizes (as used in SQL Server 2008 reports) but what I&#8217;ve seen on the net people are using cursors and temporary tables (completely unnecessary). 

> NOTE (added later):
  
> In order to update table sizes in sys.dm\_db\_partition_stats you can execute next DBCC (all tables for current database):
  
> `DBCC UPDATEUSAGE (0) WITH NO_INFOMSGS, COUNT_ROWS` 

<pre>;WITH SpaceUsage AS 
(
   SELECT s.Name + '.' + o.Name as tblName,
          case when i.index_id < 2 Then row_count else 0 end as nRows,
          reserved_page_count as Reserved,
          used_page_count     as Used,
          case when i.index_id < 2 Then (in_row_used_page_count - in_row_data_page_count) 
                                    else  p.used_page_count end as indUsed
     FROM sys.dm_db_partition_stats p
    INNER JOIN sys.objects          o ON o.object_id = p.object_id
    INNER JOIN sys.schemas          s ON s.schema_id = o.schema_id
     LEFT OUTER JOIN sys.indexes    i on i.object_id = p.object_id and i.index_id = p.index_id
    WHERE o.type = 'U'
      and o.is_ms_shipped = 0
)                                                   
Select tblName as [Table Name],
       sum(nRows)             as [#Records],
       sum(Reserved)      * 8 as [Reserved(KB)],
       sum(Used-indUsed)  * 8 as [Data(KB)],
       sum(indUsed)       * 8 as [Indexes(KB)],
       sum(Reserved-used) * 8 as [Unused(KB)]
  from SpaceUsage
 Group By tblName
 Order By [Reserved(KB)] Desc   </pre>

Here is a version with more details.

<pre>;WITH SpaceUsage AS 
(
   SELECT s.Name + '.' + o.Name as tblName,
          Case When i.index_id = 0 then 0 else 1 end as nInd,
          case when i.index_id < 2 Then row_count else 0 end as nRows,
          used_page_count     as Used,
          reserved_page_count as Reserved,
          case when i.index_id <= 1 Then in_row_data_page_count           else 0 end as rowUsed,
          case when i.index_id <= 1 Then (in_row_used_page_count - in_row_data_page_count) 
                                                                          else 0 end as cliUsed,
          case when i.index_id <  1 Then p.used_page_count                else 0 end as indUsed,
          case when i.index_id <= 1 Then lob_used_page_count              else 0 end as lobUsed,
          case when i.index_id <= 1 Then row_overflow_used_page_count     else 0 end as ofwUsed,
          case when i.index_id <= 1 Then in_row_reserved_page_count       else 0 end as rowRsvd,
          case when i.index_id <= 1 Then row_overflow_reserved_page_count else 0 end as ofwRsvd,
          case when i.index_id <= 1 Then lob_reserved_page_count          else 0 end as lobRsvd,
          case when i.index_id >  1 Then reserved_page_count              else 0 end as indRsvd
     FROM sys.dm_db_partition_stats p
    INNER JOIN sys.objects          o ON o.object_id = p.object_id
    INNER JOIN sys.schemas          s ON s.schema_id = o.schema_id
     LEFT OUTER JOIN sys.indexes    i on i.object_id = p.object_id and i.index_id = p.index_id
    WHERE o.type = 'U'
      and o.is_ms_shipped = 0
)                                                   
Select tblName,
       Sum(nInd)         as nInd,
       sum(nRows)        as nRows,
       sum(Used)     * 8 as UsedKB,
       case when sum(nRows)>0 Then (sum(Used)*8192)/Sum(nRows) else null end as avgBPR,
       sum(Reserved) * 8 as ReservedKB,
       sum(rowUsed)  * 8 as rowUsedKB,
       sum(cliUsed)  * 8 as cliUsedKB,
       sum(indUsed)  * 8 as indUsedKB,
       sum(lobUsed)  * 8 as lobUsedKB,
       sum(ofwUsed)  * 8 as ofwUsedKB,
       sum(rowRsvd)  * 8 as rowRsvdKB,
       sum(ofwRsvd)  * 8 as ofwRsvdKB,
       sum(lobRsvd)  * 8 as lobRsvdKB,
       sum(indRsvd)  * 8 as indRsvdKB
  from SpaceUsage
 Group By tblName
 Order By ReservedKB Desc  </pre>