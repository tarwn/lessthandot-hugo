---
title: Find all the tables and indexes that have data compression
author: SQLDenis
type: post
date: 2012-11-16T17:32:00+00:00
ID: 1791
excerpt: |
  I took a backup of one of our test databases today and gave it to someone so that it could be restored on one of their servers.
  
  I got back the following in an email from the person who tried to do the restore
  
  
  
  Date                      11/16/20&hellip;
url: /index.php/datamgmt/datadesign/find-all-the-tables-and/
views:
  - 35103
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server Admin
tags:
  - compression
  - how to
  - indexing
  - partitioning
  - sql server 2008
  - sql server 2012

---
I took a backup of one of our test databases today and gave it to someone so that it could be restored on one of their servers.

I got back the following in an email from the person who tried to do the restore

_</p> 

Date 11/16/2012 12:58:16 PM
  
Log SQL Server (Current – 11/16/2012 1:00:00 PM)

Source spid76

Message
  
Database 'YourCrappyDB' cannot be started in this edition of SQL Server because part or all of object 'CrappyIndexData' is enabled with data compression or vardecimal storage format. Data compression and vardecimal storage format are only supported on SQL Server Enterprise Edition.</em>

Okay, so they are running the standard edition of SQL Server. How can you quickly find all the tables and indexes that use compression? Let's take a look, first we are going to create three tables, a heap, a table with a non clustered index and a table with a clustered index

A table without indexes (a heap)

sql
CREATE TABLE TestCompress(SomeCol VARCHAR(1000))
GO

ALTER TABLE TestCompress
REBUILD PARTITION = ALL WITH (DATA_COMPRESSION =  PAGE)
```
A table with a non clustered index

sql
--Non clustered index
CREATE TABLE TestCompress2(SomeCol VARCHAR(100) NOT null)
GO

CREATE NONCLUSTERED INDEX IX_TestCompress2 
    ON TestCompress2 (SomeCol)
WITH ( DATA_COMPRESSION = ROW ) ; 
GO
```

A table with a clustered index

sql
--Clustered index
CREATE TABLE TestCompress3(SomeCol VARCHAR(100) NOT null)
GO

CREATE CLUSTERED INDEX IX_TestCompress3 
    ON TestCompress3 (SomeCol)
WITH ( DATA_COMPRESSION = ROW ) ; 
GO
```

Here is the query that will give you the table name, the storage type, the index name if there is one and the type of compression

sql
SELECT DISTINCT
SCHEMA_NAME(o.schema_id)  + '.' + OBJECT_NAME(o.object_id) AS TableName,
i.name AS IndexName,
p.data_compression_desc AS CompressionType,
i.type_desc AS StorageType
FROM sys.partitions  p 
INNER JOIN sys.objects o 
ON p.object_id = o.object_id 
JOIN sys.indexes i 
ON p.object_id = i.object_id
AND i.index_id = p.index_id
WHERE p.data_compression > 0 
AND SCHEMA_NAME(o.schema_id) <> 'SYS' 
```

Here is the output of that query

<div class="tables">
  <table>
    <tr>
      <th>
        TableName
      </th>
      
      <th>
        IndexName
      </th>
      
      <th>
        CompressionType
      </th>
      
      <th>
        StorageType
      </th>
    </tr>
    
    <tr>
      <td>
        dbo.TestCompress
      </td>
      
      <td>
        NULL
      </td>
      
      <td>
        PAGE
      </td>
      
      <td>
        HEAP
      </td>
    </tr>
    
    <tr>
      <td>
        dbo.TestCompress2
      </td>
      
      <td>
        IX_TestCompress2
      </td>
      
      <td>
        ROW
      </td>
      
      <td>
        NONCLUSTERED
      </td>
    </tr>
    
    <tr>
      <td>
        dbo.TestCompress3
      </td>
      
      <td>
        IX_TestCompress3
      </td>
      
      <td>
        ROW
      </td>
      
      <td>
        CLUSTERED
      </td>
    </tr>
  </table>
</div>

Now why do I have a distinct in my query? The reason is that if you have your indexes/tables partitioned you will get more than one row per index. you can add p.rows to the select portion of the queries and you will see how many rows each partition holds

Hopefully this will help someone in the future