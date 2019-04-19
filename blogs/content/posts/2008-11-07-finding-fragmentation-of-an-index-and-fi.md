---
title: Finding Fragmentation Of An Index And Fixing It
author: SQLDenis
type: post
date: 2008-11-07T14:45:12+00:00
ID: 198
excerpt: |
  A lof of time your index will get fragmented over time if you do a lot of updates or inserts and deletes.
  We will look at an example by creating a table, fragmenting the heck out of it and then doing a reorganize and rebuild on the index.
  
  First crea&hellip;
url: /index.php/datamgmt/datadesign/finding-fragmentation-of-an-index-and-fi/
views:
  - 18876
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - fragmentation
  - indexing
  - maintenance
  - sql server 2005
  - sql server 2008

---
A lof of time your index will get fragmented over time if you do a lot of updates or inserts and deletes.
  
We will look at an example by creating a table, fragmenting the heck out of it and then doing a reorganize and rebuild on the index.

First create this table

```sql
CREATE TABLE TestIndex (name1 varchar(500) 
not null,id int 
not null,userstat  int not null,
name2 varchar(500) not null,
SomeVal uniqueidentifier not null)
```

Now insert 50000 rows

```sql
INSERT TestIndex
SELECT top 50000 s.name,s.id,s.userstat,s2.name,newid() 
FROM master..sysobjects s
CROSS JOIN master..sysobjects s2
```

Now create this index

```sql
CREATE CLUSTERED INDEX IX_TestIndex_Index ON TestIndex(SomeVal)
```

Now let us look at some data by using the sys.dm\_db\_index\_physical\_stats DMV. Keep this query handy, we will run it many times

```sql
SELECT Object_name(object_id) as Tablename,s.name as Indexname
,index_type_desc
,avg_fragmentation_in_percent
,page_count
FROM sys.dm_db_index_physical_stats (DB_ID(), NULL, NULL , NULL, N'LIMITED') d
join sysindexes s on d.object_id = s.id
and d.index_id = s.indid
and s.name ='IX_TestIndex_Index'
```

(Result Set)

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

That is good, almost no fragmentation. Let's change that shall we?

```sql
UPDATE TestIndex
SET SomeVal = NEWID()
```

Okay, now you can see that the index is completely fragmented, we are also using 955 pages to store the data instead of 451
  
(Result Set)

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

There are two ways to fix fragmentation, one is to reorganize the index and the other is to rebuild the index. Reorganize is an online operation while rebuild is not unless you specify ONLINE = ON, ONLINE = ON will only work on Enterprise editions of SQL Server.
  
Here is how to do a reorganize

```sql
ALTER INDEX IX_TestIndex_Index ON TestIndex
REORGANIZE;
```

(Result Set)

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

As you can see after the reorganize(DBCC INDEXDEFRAG for you SQL Server 2000 folks) fragmentation levels dropped to less than 3 percent.

Just for fun let's also rebuild (Drop and recreate the index for you SQL Server 2000 folks) the index

```sql
ALTER INDEX IX_TestIndex_Index ON TestIndex
REBUILD;
```

(Result Set)

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

As you can see the rebuild made fragmentation almost 0

Here are two differences between REBUILD ONLINE = ON and REBUILD ONLINE = OFF
  
**ON**
  
Long-term table locks are not held for the duration of the index operation. During the main phase of the index operation, only an Intent Share (IS) lock is held on the source table. This allows queries or updates to the underlying table and indexes to continue. At the start of the operation, a Shared (S) lock is very briefly held on the source object. At the end of the operation, an S lock is very briefly held on the source if a nonclustered index is being created, or an SCH-M (Schema Modification) lock is acquired when a clustered index is created or dropped online, or when a clustered or nonclustered index is being rebuilt. ONLINE cannot be set to ON when an index is being created on a local temporary table.

**OFF**
  
Table locks are applied for the duration of the index operation. An offline index operation that creates, rebuilds, or drops a clustered, spatial, or XML index, or rebuilds or drops a nonclustered index, acquires a Schema modification (Sch-M) lock on the table. This prevents all user access to the underlying table for the duration of the operation. An offline index operation that creates a nonclustered index acquires a Shared (S) lock on the table. This prevents updates to the underlying table but allows read operations, such as SELECT statements.

Of course you will not run rebuild/reorganize manually for every index in your database, Michelle Ufford from the <a href="http://sqlfool.com" class="external text" title="http://sqlfool.com" rel="nofollow">SQL Fool</a> blog has a nice post with just a script which can do this automatically, you can find that here: <a href="http://sqlfool.com/?p=63" class="external text" title="http://sqlfool.com/?p=63" rel="nofollow">Index Defrag Script</a> 

## Permissions

I added this section because our [SQLCop][1] tool has an index fragmentation check, unfortunately if you don't have permissions you get an error. Big thanks to [George][2] for taking the time to write this section.

In order to run the query that checks for fragmented indexes, you need to have VIEW DATABASE STATE permissions.

To determine if you have this permission:

```sql
IF Exists(SELECT 1 FROM fn_my_permissions (NULL, 'DATABASE') WHERE permission_name = 'VIEW DATABASE STATE')
  SELECT 'You have permission'
ELSE
  SELECT 'You do not have permission'
```
If you do not have permissions, a security admin on your server can grant you permissions with the following query:

```sql
GRANT VIEW DATABASE STATE TO YourLoginName
```

 

You can also deny this permission to a user with the following query:

```sql
DENY VIEW DATABASE STATE TO YourLoginName
```

This post is also on our [SQL Server Admin Hacks][3]

 [1]: http://sqlcop.ltd.local/
 [2]: /index.php/All/?disp=authdir&author=10
 [3]: http://wiki.ltd.local/index.php/SQL_Server_Admin_Hacks