---
title: 'SQL Advent 2011 Day 24: Index REBUILD and REORGANIZE'
author: SQLDenis
type: post
date: 2011-12-24T11:47:00+00:00
excerpt: 'Today we are going to look at how to recreate and defragment indexes. Back in the SQL Server 2000 days you would do a CREATE INDEX WITH DROP_EXISTING/DBCC DBREINDEX  or DBCC INDEXDEFRAG. To check fragmentation, you would use DBCC SHOWCONTIG. To check fragmentation in SQL Server 2005 and up, you now can use the Dynamic Management View sys.dm_db_index_physical_stats.  To rebuild an index, you use ALTER INDEX IndexName REBUILD; to defragment and index, you use ALTER INDEX IndexName REORGANIZE;'
url: /index.php/datamgmt/datadesign/index-rebuild-and-reorganize-sql/
views:
  - 10434
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - indexing
  - sql server 2005
  - sql server 2008
  - sql server 2012

---
In my [Are you ready for SQL Server 2012 or are you still partying like it is 1999?][1] post, I wrote about how you should start using SQL Server 2005 and SQL Server 2008 functionality now in order to prepare for SQL Server 2012. I still see tons of code that is written in the pre 2005 style and people still keep using those functions, procs and statements even though SQL Server 2005 and 2008 have much better functionality.

This is the third indexing post in this series, we already looked at [Filtered Indexes][2] and [Indexes with Included Columns][3]. Today we are going to look at how to recreate and defragment indexes. Back in the SQL Server 2000 days you would do a CREATE INDEX WITH DROP\_EXISTING/DBCC DBREINDEX or DBCC INDEXDEFRAG. To check fragmentation, you would use DBCC SHOWCONTIG. To check fragmentation in SQL Server 2005 and up, you now can use the Dynamic Management View sys.dm\_db\_index\_physical_stats. To rebuild an index, you use _ALTER INDEX IndexName REBUILD;_ to defragment and index, you use _ALTER INDEX IndexName REORGANIZE_;

Let&#8217;s write some T-SQL to see this all in action, first create this table.

<pre>CREATE TABLE TestIndex (name1 varchar(500)not null,
id int not null,
userstat int not null,
name2 varchar(500) not null,
SomeVal uniqueidentifier not null)</pre>

Now insert 50000 rows

<pre>INSERT TestIndex
SELECT top 50000 s.name,s.id,s.userstat,s2.name,newid() 
FROM master..sysobjects s
CROSS JOIN master..sysobjects s2</pre>

Now create this index

<pre>CREATE CLUSTERED INDEX IX_TestIndex_Index ON TestIndex(SomeVal)</pre>

Now let us look at some data by using the sys.dm\_db\_index\_physical\_stats Dynamic Management View. 

<pre>SELECT Object_name(object_id) as Tablename,s.name as Indexname
,index_type_desc
,avg_fragmentation_in_percent
,page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') d
join sysindexes s on d.object_id = s.id
and d.index_id = s.indid
and s.name ='IX_TestIndex_Index'</pre>

(Result Set) 

<div class="tables">
  <table border="1">
    <tr>
      <th>
        Tablename
      </th>
      
      <th>
        Indexname
      </th>
      
      <th>
        index_type_desc
      </th>
      
      <th>
        avg_fragmentation_in_percent
      </th>
      
      <th>
        page_count
      </th>
    </tr>
    
    <tr>
      <td>
        TestIndex
      </td>
      
      <td>
        IX_TestIndex_Index
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
        0.22172949002217296
      </td>
      
      <td>
        451
      </td>
    </tr>
  </table>
</div>

That is good, almost no fragmentation, let&#8217;s change that shall we? You remember [not to cluster an index on a uniqueidentifier when using the NEWID function][4] right? That will completely fragment your index because of page splits

<pre>UPDATE TestIndex
SET SomeVal = NEWID()</pre>

Okay, now you can see that the index is completely fragmented, we are also using 955 pages to store the data instead of 451
  
(Result Set) 

<div class="tables">
  <table border="1">
    <tr>
      <th>
        Tablename
      </th>
      
      <th>
        Indexname
      </th>
      
      <th>
        index_type_desc
      </th>
      
      <th>
        avg_fragmentation_in_percent
      </th>
      
      <th>
        page_count
      </th>
    </tr>
    
    <tr>
      <td>
        TestIndex
      </td>
      
      <td>
        IX_TestIndex_Index
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
        99.3717277486911
      </td>
      
      <td>
        955
      </td>
    </tr>
  </table>
</div>

There are two ways to fix fragmentation, one is to reorganize the index and the other is to rebuild the index. REORGANIZE is an online operation while REBUILD is not unless you specify ONLINE = ON, ONLINE = ON will only work on Enterprise editions of SQL Server. If you rebuild the index with the ON option then your data is available while that is running, if it set to OFF then the data is not available while the rebuild is running. You can also stop a REORGANIZE of the index at any time.
  
Here is how to do a REORGANIZE

<pre>ALTER INDEX IX_TestIndex_Index ON TestIndex
REORGANIZE;</pre>

(Result Set) 

<div class="tables">
  <table border="1">
    <tr>
      <th>
        Tablename
      </th>
      
      <th>
        Indexname
      </th>
      
      <th>
        index_type_desc
      </th>
      
      <th>
        avg_fragmentation_in_percent
      </th>
      
      <th>
        page_count
      </th>
    </tr>
    
    <tr>
      <td>
        TestIndex
      </td>
      
      <td>
        IX_TestIndex_Index
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
        2.8824833702882482
      </td>
      
      <td>
        451
      </td>
    </tr>
  </table>
</div>

As you can see after the reorganize(DBCC INDEXDEFRAG for you SQL Server 2000 folks) fragmentation levels dropped to less than 3 percent.

Just for fun let&#8217;s also rebuild (Drop and recreate/DBCC REINDEX for you SQL Server 2000 folks) the index

<pre>ALTER INDEX IX_TestIndex_Index ON TestIndex
REBUILD;</pre>

(Result Set)

<div class="tables">
  <table border="1">
    <tr>
      <th>
        Tablename
      </th>
      
      <th>
        Indexname
      </th>
      
      <th>
        index_type_desc
      </th>
      
      <th>
        avg_fragmentation_in_percent
      </th>
      
      <th>
        page_count
      </th>
    </tr>
    
    <tr>
      <td>
        TestIndex
      </td>
      
      <td>
        IX_TestIndex_Index
      </td>
      
      <td>
        CLUSTERED INDEX
      </td>
      
      <td>
        0.22222222222222221
      </td>
      
      <td>
        450
      </td>
    </tr>
  </table>
</div>

As you can see the rebuild made fragmentation almost 0.

You can rebuild an index ONLINE (enterprise edition only) or OFFLINE, OFFLINE is the default

Here are two differences between REBUILD ONLINE = ON and REBUILD ONLINE = OFF
  
**ON**
  
Long-term table locks are not held for the duration of the index operation. During the main phase of the index operation, only an Intent Share (IS) lock is held on the source table. This allows queries or updates to the underlying table and indexes to continue. At the start of the operation, a Shared (S) lock is very briefly held on the source object. At the end of the operation, an S lock is very briefly held on the source if a nonclustered index is being created, or an SCH-M (Schema Modification) lock is acquired when a clustered index is created or dropped online, or when a clustered or nonclustered index is being rebuilt. ONLINE cannot be set to ON when an index is being created on a local temporary table.

**OFF**
  
Table locks are applied for the duration of the index operation. An offline index operation that creates, rebuilds, or drops a clustered, spatial, or XML index, or rebuilds or drops a nonclustered index, acquires a Schema modification (Sch-M) lock on the table. This prevents all user access to the underlying table for the duration of the operation. An offline index operation that creates a nonclustered index acquires a Shared (S) lock on the table. This prevents updates to the underlying table but allows read operations, such as SELECT statements.

Of course you will not run rebuild/reorganize manually for every index in your database, Michelle Ufford from the <a href="http://sqlfool.com" class="external text" title="http://sqlfool.com" rel="nofollow">SQL Fool</a> blog has a nice post with just a script which can do this automatically, you can find that here: <a href="http://sqlfool.com/2011/06/index-defrag-script-v4-1/" class="external text" title="http://sqlfool.com/?p=63" rel="nofollow">Index Defrag Script</a> 

## Bonus, are my indexes used and is it mostly seeks or scans?. 

The sys.dm\_db\_index\_usage\_stats dynamic management view is extremely helpful in a couple of ways. It can help you identify if an index is used or not. You can also find out the scan to seek ratio. Another helpful thing is the fact that the last seek and scan dates are in the view, this can help you determine if the index is still being used. Keep in mind that when you restart the SQL instance the data is wiped out. 

Run the query below

<pre>SELECT
TableName = OBJECT_NAME(s.[OBJECT_ID]),
IndexName = i.name,
s.last_user_seek,
s.user_seeks,
CASE s.user_seeks WHEN 0 THEN 0
ELSE s.user_seeks*1.0 /(s.user_scans + s.user_seeks) * 100.0 END AS SeekPercentage,
s.last_user_scan,
s.user_scans,
CASE s.user_scans WHEN 0 THEN 0
ELSE s.user_scans*1.0 /(s.user_scans + s.user_seeks) * 100.0 END AS ScanPercentage,
s.last_user_lookup,
s.user_lookups,
s.last_user_update,
s.user_updates,
s.last_system_seek,
s.last_system_scan,
s.last_system_lookup,
s.last_system_update,*
FROM
sys.dm_db_index_usage_stats s
INNER JOIN
sys.indexes i
ON
s.[OBJECT_ID] = i.[OBJECT_ID]
AND s.index_id = i.index_id
WHERE
s.database_id = DB_ID()
AND OBJECTPROPERTY(s.[OBJECT_ID], 'IsMsShipped') = 0;</pre>

Note that every individual seek, scan, lookup, or update on the specified index by one query execution is counted as a use of that index and increments the corresponding counter in this view. Information is reported both for operations caused by user-submitted queries, and for operations caused by internally generated queries, such as scans for gathering statistics.

The user_updates counter indicates the level of maintenance on the index caused by insert, update, or delete operations on the underlying table or view. You can use this view to determine which indexes are used only lightly all by your applications. You can also use the view to determine which indexes are incurring maintenance overhead. You may want to consider dropping indexes that incur maintenance overhead, but are not used for queries, or are only infrequently used for queries.

That is it for SQL Advent 2011, come back tomorrow for the recap and I will also have a post pointing to my SQL 2012 posts so that you can look at what is coming in the next year

 [1]: /index.php/DataMgmt/DataDesign/are-you-ready-for-sql
 [2]: /index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/sql-advent-2011-day-19
 [3]: /index.php/DataMgmt/DataDesign/sql-advent-2011-day-20
 [4]: /index.php/DataMgmt/DBProgramming/best-practice-don-t-not-cluster-on-uniqu