---
title: Finding Exact Duplicate Indexes
author: George Mastros (gmmastros)
type: post
date: 2012-04-12T17:14:00+00:00
ID: 1596
excerpt: 'This blog is not meant to be a comprehensive explanation of indexes.  It is meant to help you determine if there are duplicate indexes within your database.  There appears to be some debate regarding what is a duplicate index.  This article defines an “&hellip;'
url: /index.php/datamgmt/datadesign/finding-exact-duplicate-indexes/
views:
  - 15294
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
This blog is not meant to be a comprehensive explanation of indexes. It is meant to help you determine if there are duplicate indexes within your database. There appears to be some debate regarding what is a duplicate index. This article defines an “Exact Duplicate Index” where there are multiple non-clustered indexes with exactly the same keys and include columns.

**This query will ignore clustered indexes.**
  
It is possible to create a non-clustered index with keys that match a clustered index. Sometimes, this could be considered a duplicate index, and sometimes it should not be considered a duplicate. For example, suppose you have a wide table so that there is only one row per data page. Also suppose you have a query that only needs to use the key columns. It will be faster to use a non-clustered index for the query instead of the clustered index because the non-clustered index would only include the key columns, and you will get more columns per data page. The non-clustered index would require less file I/O, and therefore increased performance.

If you have a wide table with many rows, a non-clustered index that matches the key columns of the clustered index will improve performance.

**This query will ignore partial duplicates with similar keys.**
  
It is possible to create indexes with similar keys, and sometimes it is beneficial to do so. For example, suppose you had an index with key columns (Col1, Col2) and another with (Col1, Col2, Col3). You probably don't need the first index because the second index will suffice. However, there are cases where Col3 could be wide and using it would cause extra I/O compared to the first index. It is probably rare that you would see any difference in performance between these indexes. I will be writing another article that explains this in depth and provides a query that will return these possible duplicates.

**This query will ignore partial duplicates with the same keys and similar includes.**
  
Column ordering for include columns does not matter, but which columns are included can make a big difference in the performance of a query. There could be similar indexes that have the same key columns but different include columns. This can affect the size of the index and cause more I/O. There will be another blog that explains this in depth and provides a query to identify duplicate indexes with similar includes.

**The query to identify exact duplicates:**

```sql
; With IndexColumns As
(
	select	I.object_id,
			I.index_id,
			SubString(
				(
					select	',' + Convert(VarChar(10), Column_id)
					from	sys.index_columns as k
					where	k.object_id = i.object_id
							and k.index_id = i.index_id
							and is_included_column = 0
					Order By key_ordinal
					for xml path('')
				), 2, 1000) As KeyColumns,
			SubString(Coalesce(
				(
					select	',' + Convert(VarChar(10), Column_id)
					from	sys.index_columns as k
					where	k.object_id = i.object_id
							and k.index_id = i.index_id
							and is_included_column = 1
					Order By column_id
					for xml path('')
				), ''), 2, 1000) As IncludeColumns
	From	sys.indexes As I
			Inner Join sys.Tables As T
				On I.object_id = T.object_id
	Where	I.type_desc <> 'Clustered'
			And T.is_ms_shipped = 0
)
Select  Object_Name(AIndex.object_id) As TableName,
        AIndex.name As Index1,
        bIndex.Name As Index2,
        'Drop Index [' + bIndex.Name + '] On [' + Object_Name(AIndex.object_id) + ']' As DropCode
From	IndexColumns As A
		Inner Join IndexColumns  As B
			On A.object_id = B.object_id
			And A.index_id < B.index_id
			And A.KeyColumns = B.KeyColumns
			And A.IncludeColumns = B.IncludeColumns
		Inner Join sys.indexes As AIndex
			On A.object_id = AIndex.object_id
			And A.index_id = AIndex.index_id
		Inner Join sys.indexes As BIndex
			On B.object_id = BIndex.object_id
			And B.index_id = BIndex.index_id 
```
When you run this query, you will see two columns in the output showing you the duplicates. Since these are exact duplicates, you can drop one of them. Which one you drop is up to you.