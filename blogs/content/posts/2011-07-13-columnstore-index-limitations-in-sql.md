---
title: ColumnStore Index limitations in SQL Server Denali CTP3
author: SQLDenis
type: post
date: 2011-07-14T00:22:00+00:00
ID: 1252
excerpt: |
  If you have been working with Sybase IQ then you might be familiar with what a columnstore database is.
  
  SQL Server has added a new type of index which is column based instead of row based, this is the columnstore index. I will create another post thi&hellip;
url: /index.php/datamgmt/datadesign/columnstore-index-limitations-in-sql/
views:
  - 12092
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - apollo
  - columnstore
  - denali
  - olap
  - oltp

---
If you have been working with [Sybase IQ][1] then you might be familiar with what a columnstore database is.

SQL Server has added a new type of index which is column based instead of row based, this is the columnstore index. I will create another post this week that will show you how to create such an index, right now I will only show you the difference between a query against a regular index and against a columnstore index, this way you can get an idea of the performance difference.

Both of these tables have identical data and have 1 million rows.

sql
SELECT COUNT(*),SomeValue FROM TestRowStore 
group by SomeValue

SELECT COUNT(*),SomeValue FROM TestColumnStore 
group by SomeValue
```

Here is the execution plan for this query, click on the image for a larger size

[<img src="http://farm7.static.flickr.com/6021/5935723862_8f99cca495_b.jpg" width="1024" height="261" alt="ColumnStore Index Execution Plan" />][2]

As you can see the columnstore index performs better.

Here are the reads

_Table 'TestRowStore'. Scan count 3, **logical reads 9636**, physical reads 0, read-ahead reads 0
  
Table 'TestColumnStore'. Scan count 2, **logical reads 8**, physical reads 0, read-ahead reads 0_

The difference in reads is dramatic

And here are the execution times

 _SQL Server Execution Times:
     
CPU time = 328 ms, elapsed time = **185 ms**.</p> 

SQL Server Execution Times:
     
CPU time = 0 ms, elapsed time = **3 ms**.</em>
  

  
This is all cool right? Unfortunately, there are things you cannot do (yet) when using a columnstore index.
  
A table with a columnstore index is read only, and cannot be updated(another post will talk about using partitions to makes this less painful)
  
Right now, a columnstore index supports only common business data types like int, real, string, money, datetime, the decimal data type has to be less than 18 digits.
  
You cannot truncate a table with a columnstore index

I decided to look in the sysmessages table for any messages that had columnstore in the description

sql
select  description  from sys.sysmessages
where msglangid = 1033
and description like'%columnstore%'
```

Here is the whole list of errors that SQL Server might throw when you try to do things that are not supported. Keep in mind that sysmessages only returns the first 255 characters so some of the messages are cut


_ 

  * SQL Server cannot load database '%.*ls' because it contains a columnstore index. The currently installed edition of SQL Server does not support columnstore indexes. Either disable the columnstore index in the database by using a supported edition of SQL S
  * The Cross Rowset check on columnstore index object ID %d, index ID %d, partition ID %I64d. Drop and recreate the columnstore index.
  * CREATE INDEX statement failed because a columnstore index cannot be unique. Create the columnstore index without the UNIQUE keyword or create a unique index without the COLUMNSTORE keyword.
  * CREATE INDEX statement failed because specifying sort order (ASC or DESC) is not allowed when creating a columnstore index. Create the columnstore index without specifying a sort order.
  * CREATE INDEX statement failed because a columnstore index cannot be created on a view. Consider creating a columnstore index on the base table or creating an index without the COLUMNSTORE keyword on the view.
  * CREATE INDEX statement failed because column '%.\*ls' on table '%.\*ls' is a computed column and a columnstore index cannot be created on a computed column. Consider creating a nonclustered columnstore index on a subset of columns that does not include the 
  * CREATE INDEX statement failed because a columnstore index cannot be a filtered index. Consider creating a columnstore index without the predicate filter.
  * CREATE INDEX statement failed because a columnstore index cannot be created on a sparse column. Consider creating a nonclustered columnstore index on a subset of columns that does not include any sparse columns.
  * CREATE INDEX statement failed because a columnstore index cannot have included columns. Create the columnstore index on the desired columns without specifying any included columns.
  * CREATE INDEX statement failed because a columnstore index cannot be created on a column with filestream data. Consider creating a nonclustered columnstore index on a subset of columns that does not include any columns with filestream data.
  * CREATE INDEX statement failed because specifying FILESTREAM\_ON is not allowed when creating a columnstore index. Consider creating a columnstore index on columns without filestream data and omit the FILESTREAM\_ON specification.
  * CREATE INDEX statement failed because a columnstore index cannot be created on a column set. Consider creating a nonclustered columnstore index on a subset of columns in the table that does not contain a column set or any sparse columns.
  * CREATE INDEX statement failed because a columnstore index cannot be created in this edition of SQL Server. See Books Online for more details on feature support in different SQL Server editions.
  * CREATE INDEX statement failed because a columnstore index must be partition-aligned with the base table. Create the columnstore index using the same partition function and same (or equivalent) partition scheme as the base table. If the base table is not 
  * CREATE INDEX statement failed because specifying %S\_MSG is not allowed when creating a columnstore index. Consider creating a columnstore index without specifying %S\_MSG.
  * CREATE INDEX statement failed because the %S\_MSG option is not allowed when creating a columnstore index. Create the columnstore index without specifying the %S\_MSG option.
  * CREATE INDEX statement failed because specifying DATA\_COMPRESSION is not allowed when creating a columnstore index. Consider creating a columnstore index without specifying DATA\_COMPRESSION. Columnstore indexes are always compressed automatically.
  * ALTER TABLE statement failed because the definition of a column cannot be changed if the column is part of a columnstore index. Consider dropping the columnstore index, altering the column, then creating a new columnstore index.
  * ALTER INDEX statement failed because a columnstore index cannot be reorganized. Reorganization of a columnstore index is not necessary.
  * ALTER INDEX REBUILD statement failed because specifying %S\_MSG is not allowed when rebuilding a columnstore index. Rebuild the columnstore index without specifying %S\_MSG.
  * ALTER INDEX REBUILD statement failed because the %S\_MSG option is not allowed when rebuilding a columnstore index. Rebuild the columnstore index without specifying the %S\_MSG option.
  * ALTER INDEX REBUILD statement failed because specifying DATA\_COMPRESSION is not allowed when rebuilding a columnstore index. Rebuild the columnstore index without specifying DATA\_COMPRESSION. Columnstore indexes are always compressed automatically.
  * %S\_MSG statement failed because data cannot be updated in a table with a columnstore index. Consider disabling the columnstore index before issuing the %S\_MSG statement, then rebuilding the columnstore index after %S_MSG is complete.
  * DBCC DBREINDEX failed because specifying FILLFACTOR is not allowed when creating or rebuilding a columnstore index. Rebuild the columnstore index without specifying FILLFACTOR.
  * CREATE INDEX statement failed because specifying key list is not allowed when creating a clustered columnstore index. Create the clustered columnstore index without specifying key list.
  * UPDATE STATISTICS failed because statistics cannot be updated on a columnstore index. UPDATE STATISTICS is valid only when used with the STATS_STREAM option.
  * Clustered columnstore index is not supported.
  * Multiple nonclustered columnstore indexes are not supported.
  * Conversion between columnstore index and relational index is not supported.
  * CREATE INDEX statement failed because a columnstore index cannot be created on a column with datatype decimal or numeric that has a precision that requires more than 8 bytes of storage. Consider either reducing the precision of column '%.*ls' to 18 or cre
  * CREATE INDEX statement failed because a columnstore index cannot be created on a datetimeoffset type with precision that requires more than 8 bytes of storage. Consider either reducing the precision of column '%.*ls' to datetimeoffset(n) where n = 0, 1, o
  * Cannot include column '%.*ls' in a columnstore index because the data type of the column is not supported in a columnstore index. The column may have been included explicitly (in the CREATE INDEX statement) or implicitly. Implicit inclusion occurs when c
  * MERGE clause of ALTER PARTITION statement failed because two nonempty partitions containing a columnstore index cannot be merged. Consider disabling the columnstore index before issuing the ALTER PARTITION statement, then rebuilding the columnstore index 
  * MERGE clause of ALTER PARTITION statement failed because two partitions on different filegroups cannot be merged if either partition contains columnstore index data. Consider disabling the columnstore index before issuing the ALTER PARTITION statement, th
  * SPLIT clause of ALTER PARTITION statement failed because the partition is not empty. Only empty partitions can be split in when a columnstore index exists on the table. Consider disabling the columnstore index before issuing the ALTER PARTITION statement
  * The stored procedure, sp_tableoption failed because a table with a nonclustered columnstore index cannot be altered to use vardecimal storage format. Consider dropping the columnstore index.
  * CREATE INDEX statement failed because table '%.*ls' uses vardecimal storage format. A columnstore index cannot be created on a table using vardecimal storage. Consider rebuilding the table without vardecimal storage.
  * TRUNCATE TABLE statement failed because table '%.*ls' has a columnstore index on it. A table with a columnstore index cannot be truncated. Consider dropping the columnstore index then truncating the table.
  * CREATE INDEX statement failed because a columnstore index on a partitioned table must be partition-aligned with the base table. Consider dropping the columnstore index before creating a new clustered index.
  * DROP INDEX statement failed because a columnstore index on a partitioned table must be partition-aligned with the base table (heap). Consider dropping the columnstore index before dropping a clustered index.
  * %S_MSG statement failed because the operation cannot be performed online on a table with a columnstore index. Perform the operation without specifying the ONLINE option or drop (or disable) the columnstore index before performing the operation using the O
  * %s cannot be enabled on a table with a columnstore index. Consider dropping columnstore index '%s' on table '%s'.
  * CREATE INDEX statement failed because a columnstore index cannot be created on a table enabled for %S\_MSG. Consider disabling %S\_MSG and then creating the columnstore index.

</em>

So with all these limitations why would you want to use this kind of index? This index is not for your typical OLTP workload, it is for people that want OLAP response time without having to create OLAP cubes, summary tables and indexed views. 

I can already see a couple of places that I will be using columnstore indexes

 [1]: /index.php/DataMgmt/DataDesign/sybase-iq-is-a-columnar-database-why-sho
 [2]: http://www.flickr.com/photos/denisgobo/5935723862/sizes/l/in/photostream/ "ColumnStore Index by Denis Gobo, on Flickr"