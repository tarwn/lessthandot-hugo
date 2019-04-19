---
title: Dealing with the could not allocate new page for database 'TEMPDB'. There are no more pages available in filegroup DEFAULT error message
author: SQLDenis
type: post
date: 2009-07-21T18:01:51+00:00
ID: 518
url: /index.php/datamgmt/datadesign/could-not-allocate-new-page-for-database/
views:
  - 28045
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - database
  - error
  - fix
  - gotcha
  - how to
  - sql
  - sql server
  - tempdb

---
Someone was writing some queries that brought back a lot of data (and I mean a LOT!!) and after a while he got the following message

> Connectivity error: \[Microsoft\]\[ODBC SQL Server Driver\][SQL Server]Could not allocate new page for database 'TEMPDB'. There are no more pages available in filegroup DEFAULT. Space can be created by dropping objects, adding additional files, or or allowing file growth

That is not good, fortunately this wasn't one of our production machines but a dev/test box.

So what causes this? You might think that tempdb is only used for temporary(#temp or ##temp) tables but that is not true, tempdb is used for a lot of thing and since SQL server 2005 it is used more than ever. Here are some things that tempdb is used for:

  * If you do any ordering on a query and this needs more memory than you have available in RAM it will also go to tempdb
  * If you have resultsets that are large and you use unions, group by, outer joins, cursors etc
  * If you use temporary tables
  * Uncommited transaction that have not been rolled back
  * DBCC CHECKDB will use the tempdb, the larger your database the more space DBCC CHECKDB will need from tempdb 
    If you are creating or rebuilding indexes with the SORT\_IN\_TEMPDB = ON option
    
    Here is some sample code that demonstrates that
    
    sql
USE AdventureWorks;
    GO
    ALTER INDEX ALL ON Production.Product
    REBUILD WITH (FILLFACTOR = 80, SORT_IN_TEMPDB = ON,
                  STATISTICS_NORECOMPUTE = ON);
    GO
```

    See also [tempdb and Index Creation][1] in Books On Line
  
    Here is an excerpt
    
    > If the SORT\_IN\_TEMPDB option is set to ON and tempdb is on a separate set of disks from the destination filegroup, during the first phase, the reads of the data pages occur on a different disk from the writes to the sort work area in tempdb. This means the disk reads of the data keys generally continue more serially across the disk, and the writes to the tempdb disk also are generally serial, as do the writes to build the final index. Even if other users are using the database and accessing separate disk addresses, the overall pattern of reads and writes are more efficient when SORT\_IN\_TEMPDB is specified than when it is not.

**How can you fix this problem?** 
  
Here are a couple of ways

  1. Restart the SQL Server service, this will recreate the tempdb database
  2. Add another file on another disk with more space
  3. Shrink the log file of tempdb

Now most likely you want to put in place some long term solutions after you did a quick fix. First of all, you should make sure that autogrow is not turned off. If you run the query below you don't want to see a value of 0 in the growth column

```sql
select growth,* from master..sysaltfiles
where dbid = db_id('tempdb')
```

This query below is another way of getting the growth value for tempdb

```sql
use tempdb
go

EXEC sp_helpfile
```

You also want to make sure that tempdb is in simple recovery mode and not in full, this query will return the recovery mode for tempdb

```sql
SELECT DATABASEPROPERTYEX('tempdb','recovery')
```

Ideally you want tempdb on its own drive, doing this will also improve performance. If you can't have tempdb on its own drive then make sure you have plenty of space on that drive and if not then add one or more files on another drive.

Make sure that your queries have covering indexes, George wrote an article that explains how to create covering indexes here: [SQL Server covering indexes][2]

Most likely you don't really want to return multi million rows in 1 big chunk, try doing it in batches that are smaller. The same applies for delete statements, try to do those in chunks, this will also perform much better. 



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://msdn.microsoft.com/en-us/library/ms188281.aspx
 [2]: /index.php/DataMgmt/DataDesign/sql-server-covering-indexes
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22