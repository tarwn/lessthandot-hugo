---
title: How to get the selectivity of an index
author: SQLDenis
type: post
date: 2008-05-20T13:32:33+00:00
ID: 31
url: /index.php/datamgmt/datadesign/how-to-get-the-selectivity-of-an-index/
views:
  - 4525
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database
  - indexes
  - optimizing
  - sql
  - sqlserver

---
The selectivity of an index is extremely important. If your index is not selective enough then the optimizer will simply have to do a scan. This is also a reason why creating an index on a gender column does not make a lot of sense.

First create this table

```sql
USE tempdb
go

CREATE TABLE TestCompositeIndex (State char(2),Zip Char(5))
INSERT TestCompositeIndex VALUES('NJ','08540')
INSERT TestCompositeIndex VALUES('NJ','08540')
INSERT TestCompositeIndex VALUES('NY','10028')
INSERT TestCompositeIndex VALUES('NY','10021')
INSERT TestCompositeIndex VALUES('NY','10021')
INSERT TestCompositeIndex VALUES('NY','10021')
INSERT TestCompositeIndex VALUES('NY','10001')
INSERT TestCompositeIndex VALUES('NJ','08536')
INSERT TestCompositeIndex VALUES('NJ','08540')
```

If you have a composite index (composite means the index contains more than one column) you need to run this code.

```sql
DECLARE @Count int

SELECT DISTINCT State, Zip
FROM TestCompositeIndex;

SET @Count = @@ROWCOUNT;

 

SELECT (@Count*1.0) / COUNT(*) AS IndexSelectivity, 
COUNT(*)AS TotalCount,
@Count AS DistinctCount
FROM TestCompositeIndex;
```
Result
  
&#8212;&#8212;&#8211;
  
IndexSelectivity TotalCount DistinctCount
  
.555555555555 9 5

If you have a one column index you can use this code

```sql
SELECT (COUNT(DISTINCT State)* 1.0) / COUNT(*) AS IndexSelectivity,
COUNT(*) AS TotalCount,
COUNT(DISTINCT State) AS DistinctCount
FROM TestCompositeIndex;
```

Result
  
&#8212;&#8212;&#8211;
  
IndexSelectivity TotalCount DistinctCount
  
.222222222222 9 2