---
title: Sybase IQ Is A Columnar Database, Why Should I Care?
author: SQLDenis
type: post
date: 2008-06-27T14:15:08+00:00
ID: 77
excerpt: 'Maybe you heard about Sybase IQ and maybe you did not. So what is Sybase IQ? Sybase IQ is a columnar database. What does this mean? This mean that the data is stored in columns and not in rows. Inserts are slower that a traditional row based database bu&hellip;'
url: /index.php/datamgmt/datadesign/sybase-iq-is-a-columnar-database-why-sho/
views:
  - 25909
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
Maybe you heard about Sybase IQ and maybe you did not. So what is Sybase IQ? Sybase IQ is a columnar database. What does this mean? This mean that the data is stored in columns and not in rows. Inserts are slower that a traditional row based database but selects are many times faster (up to 50 times). The good thing about this technology is that the SQL looks the same, the only difference is that the data is stored in a different way.

Sybase announced that they have [Set Guinness World Record for World’s Largest Data Warehouse][1] 

> Powered by the category-leading column-oriented database Sybase IQ, the data warehouse is certified to support a record-breaking one petabyte of mixed relational and unstructured data—more than 34 times larger than the largest industry standard benchmark1 and twice the size of the largest commercial data warehouse known to date2. In total, the data warehouse contains six trillion rows of transactional data and more than 185 million content-searchable documents, such as emails, reports, spreadsheets and other multimedia objects. 

I have been using Sybase IQ for about 6 months now, I am not impressed with the way the tools look, hey I&#8217;ll take performance over tools any day 🙂

In SQL Server you have 2 types of indexes (not counting XML indexes) clustered and non clustered. Well Sybase IQ has a lot more

With Sybase IQ you have these indexes to choose from

**The Default column index**
  
For any column that has no index defined, or whenever it is the most effective, query results are produced using the default index. This structure is fastest for projections, but generally is slower than any of the three column index types you define for anything other than a projection. Performance is still faster than most RDBMSs since one column of data is fetched, while other RDBMSs need to fetch all columns which results in more disk I/O operations.

**The Low_Fast (LF) index type**
  
This index is ideal for columns that have a very low number of unique values (under 1,000) such as sex, Yes/No, True/False, number of dependents, wage class, and so on. LF is the fastest index in Sybase IQ.

**The High_Group (HG) index type**
  
The High\_Group index is commonly used for join columns with integer data types. It is also more commonly used than High\_Non_Group because it handles GROUP BY efficiently.

**The High\_Non\_Group (HNG) index type**
  
Add an HNG index when you need to do range searches.
  
An HNG index requires approximately three times less disk space than an HG index requires. On that basis alone, if you do not need to do group operations, use an HNG index instead of a HG index.

**The Compare (CMP) index type**
  
A Compare (CMP) index is an index on the relationship between two columns. You may create Compare indexes on any two distinct columns with identical data types, precision, and scale. The CMP index stores the binary comparison (<, >, or =) of its two columns.

**The Containment (WD) index type**
  
This index allows you to store words from a column string of CHAR and VARCHAR data.

The Date (DATE) index type
  
The Time (TIME) index type
  
The Datetime (DTTM) index type
  
Three index types are used to process queries involving date, time, or datetime quantities:

**The JOIN Index (Linear joins and Star joins)**
  
Join indexes usually provide better query performance than when table joins are first defined at query time (ad hoc joins). In many situations, however, you can gain optimal performance on joined columns without creating join indexes.

Sybase IQ does not have clustered or non clustered indexes. You can specify clustered index but it will be ignored. 

Here are two examples of creating indexes

The first index is a Low Fast index, the second index is a datetime index 

```sql
CREATE LF INDEX IX_LF_IGROUP_Index ON TestTable (SomeColumn)

CREATE DTTM INDEX IX_DTTM_Tradate_Index ON TestTable (SomeDate) 
```

If you want to know more about Sybase IQ visit this page http://www.sybase.com/products/datawarehousing/sybaseiq

 [1]: http://www.sybase.com/detail?id=1056945