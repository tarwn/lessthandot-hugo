---
title: 'Best Practice: Do not cluster on UniqueIdentifier when you use NewId'
author: George Mastros (gmmastros)
type: post
date: 2009-11-12T11:57:49+00:00
ID: 624
url: /index.php/datamgmt/dbprogramming/best-practice-don-t-not-cluster-on-uniqu/
views:
  - 26479
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
There can only be one clustered index per table because SQL Server stores the data in the table in the order of the clustered index . When you use a UniqueIdentifier as the first column in a clustered index, every time you insert a row in the table, it is almost guaranteed to be inserted in to the middle of the table. SQL server stores data in 8K pages. If a page is full, SQL Server will do a page split, which causes another 8k page to be allocated and half the data from the previous page to be moved to the new page. Individually, each page split is fast but does take a little bit of time. In a high transaction environment, there could be many page splits happening frequently, which ultimately result in slower performance. 

When you use an Identity column for a clustered index, the next value inserted is guaranteed to be higher than the previous value. This means that new rows will always be added to the end of the table and you will not get unnecessary page splits for table fragmentation.

SQL Server 2005 introduced a new function called NewSequentialId(). This function can only be used as a default for a column of type UniqueIdentifier. The benefit of NewSequentialId is that it always generates a value greater than any other value already in the table. This causes the new row to be inserted at the end of the table and therefore no page splits. 

**How to detect this problem:**

sql
Select  so.name as TableName, 
        sind.name as IndexName, 
        sik.keyno, 
        col.name as ColName,
        systypes.name,
        sind.OrigFillFactor,
        syscomments.text As ColumnDefault
from    sysobjects so
        inner join sysindexes sind 
          on so.id=sind.id
        inner join sysindexkeys sik 
          on sind.id=sik.id 
          and sind.indid=sik.indid
        inner join syscolumns col 
          on col.id=sik.id 
          and col.colid=sik.colid
        inner join systypes
          On col.xtype = systypes.xtype
        inner join syscomments
          on col.cdefault = syscomments.id
where   sind.status & 16 = 16
        and systypes.name = 'uniqueidentifier'
        and keyno = 1
        and sind.OrigFillFactor = 0
        and syscomments.text Like '%newid%'
order by so.name, sik.keyno
```
**How to correct it:** There are several ways to prevent this problem. The best method is to use NewSequentialId() instead of NewId. Alternatively (if you are using SQL 2000), you could set the fill factor for the index to be less than 100%. Fill factor identifies how &#8220;full&#8221; the data pages are when you recreate the index. With a 100% fill factor there is no room in the index to accommodate new rows. If you need to use a UniqueIdentifier, and it must be clustered and you cannot use NewSequentialId, then you should modify the Fill Factor to minimize page splits. If you do this, it&#8217;s important to rebuild the index periodically.

**Level of severity:** Moderate

**Level of difficulty:** Easy