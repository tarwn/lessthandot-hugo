---
title: A couple of things to be aware of when working with tables in SQL Azure
author: SQLDenis
type: post
date: 2012-05-02T23:25:00+00:00
ID: 1614
excerpt: |
  I was messing around with SQL Azure today and noticed a couple of things that are not supported compared to the regular version of SQL Server in regards to tables. I will list these in this post.
  
  All the code has been tested against the following ver&hellip;
url: /index.php/datamgmt/datadesign/a-couple-of-things-to/
views:
  - 17428
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - azure
  - sql azure
  - the cloud

---
I was messing around with SQL Azure today and noticed a couple of things that are not supported compared to the regular version of SQL Server in regards to tables. I will list these in this post.

All the code has been tested against the following version of SQL Azure as returned by @@version

Microsoft SQL Azure (RTM) – 11.0.1892.4
  
Apr 24 2012 10:21:54 Copyright (c) Microsoft Corporation

## 1) Tables have to have a clustered index if you want to insert data into those tables

There is actually no problem creating a table without a clustered index (heap)
  
If you run this code

```sql
CREATE TABLE Test(bla uniqueidentifier default newid())
```

And then refresh and expand the tables folder, you will see the following

[<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/AzureTable.PNG?mtime=1336007070" width="422" height="356" />][1]

Now run the following

```sql
INSERT Test DEFAULT VALUES
```

And here is the error
  
_Msg 40054, Level 16, State 1, Line 1
  
Tables without a clustered index are not supported in this version of SQL Server. Please create a clustered index and try again._

Adding a clustered index and running the insert again fixes this

```sql
CREATE CLUSTERED INDEX ix_Test_bla on Test(bla)
GO

INSERT Test DEFAULT VALUES
```

The problem is that until you try to insert data in your tables, you won't see this error, at that point it might be too late and your application will fail

To list all the tables in your database without a clustered index, you can use this code

```sql
SELECT t.name 
FROM sys.tables t
WHERE NOT EXISTS (	SELECT 1  FROM sys.indexes i
					WHERE i.type_desc = 'CLUSTERED' 
					AND i.object_id = t.object_id )
```

BTW, temporary tables can be created without a clustered index, this runs without a problem

```sql
CREATE TABLE #bla(id int)
INSERT #bla VALUES(1)
```

## 2) Global temporary tables are not supported

If you try to create the following global temporary table, you will get an error

```sql
CREATE TABLE ##bla(id int)
```

_Msg 40516, Level 15, State 1, Line 2
  
Global temp objects are not supported in this version of SQL Server._

I admit that I have only used a global temporary table once or twice in the last 10+ years or so, I don't think this is a big deal

## 3) Select Into is not supported

You cannot use select into either in SQL Azure

```sql
SELECT * INTO Testb from Test
```

Here is the error
  
_Msg 40510, Level 16, State 1, Line 1
  
Statement 'SELECT INTO' is not supported in this version of SQL Server._

I don't think this is a big deal either, sure you will have to list all the columns and create the table first before inserting, this could be a pain in the neck but it is not the end of the world

## 4) Newsequentialid() is not supported

Remember this table from before

```sql
CREATE TABLE Test2(bla uniqueidentifier default newid())
```

Try making that default newsequentialid() instead of newid()

```sql
CREATE TABLE Testb(bla uniqueidentifier default newsequentialid())
```

Here is the error message
  
_Msg 40511, Level 15, State 1, Line 1
  
Built-in function 'newsequentialid' is not supported in this version of SQL Server._

In this case, you will probably need to go with newid() instead of newsequentialid(), not a big change

## Conclusion

Overall I don't think that these are major issues and should be fairly easy to either change the design or change the code to get around these limitations in this version of SQL Azure

What do you think, big pain in the neck or no big deal?

 [1]: /wp-content/uploads/blogs/DataMgmt/Denis/AzureTable.PNG?mtime=1336007070