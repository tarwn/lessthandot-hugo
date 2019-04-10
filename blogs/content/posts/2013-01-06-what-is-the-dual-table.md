---
title: What is the Dual table in Oracle and why do I need it?
author: SQLDenis
type: post
date: 2013-01-06T13:36:00+00:00
ID: 1901
excerpt: |
  When coming from SQL Server, you might find it weird that you don't see code that looks like this
  
  
  select 2
  
  That code won't run in Oracle, unlike SQL Server, Oracle requires the use of the FROM clause in its syntax. This is why Oracle has DUAL
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/what-is-the-dual-table/
views:
  - 10312
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - Oracle
  - Oracle Admin
tags:
  - gotcha
  - oracle
  - sql server
  - tip

---
When coming from SQL Server, you might find it weird that you don't see code that looks like this in Oracle's PL/SQL

```plsql
select 2

select sysdate --  getdate()in SQL Server
```
Unlike with SQL Server that code won't run in Oracle, Oracle requires the use of the FROM clause in its syntax. This is why Oracle has the DUAL table.

If you try to run something like this

sql
select 2;
```

you will get the following error

_ORA-00923: FROM keyword not found where expected
  
00923. 00000 – “FROM keyword not found where expected”_

I decided to see where the Dual table came from.

From wikipedia

> The DUAL table was created by Charles Weiss of Oracle corporation to provide a table for joining in internal views:
> 
> I created the DUAL table as an underlying object in the Oracle Data Dictionary. It was never meant to be seen itself, but instead used inside a view that was expected to be queried. The idea was that you could do a JOIN to the DUAL table and create two rows in the result for every one row in your table. Then, by using GROUP BY, the resulting join could be summarized to show the amount of storage for the DATA extent and for the INDEX extent(s). The name, DUAL, seemed apt for the process of creating a pair of rows from just one.
> 
> The original DUAL table had two rows in it (hence its name), but subsequently it only had one row.

Running the following code

```plsql
select * from dual;
```

Give you a resultset of 1 row with 1 column named DUMMY with the value X

[<img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleDual.PNG?mtime=1357485931" width="315" height="74" />][1]

So there you have it, this is why the Dual table exists.

If you need to do something like this

sql
SELECT 3/2
```

in Oracle it needs to be 

```plsql
SELECT 3/2 from dual;
```

However Oracle returns 1.5 while SQL Server will return 1, SQL Server does integer math and Oracle does not. That is another difference you need to be aware of, this is more problematic when moving from Oracle SQL Server and then wondering where all the decimals went.

 [1]: /wp-content/uploads/blogs/DataMgmt/Denis/Oracle/OracleDual.PNG?mtime=1357485931