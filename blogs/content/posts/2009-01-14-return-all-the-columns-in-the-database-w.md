---
title: Return all the columns in the database which allow NULLS
author: SQLDenis
type: post
date: 2009-01-14T17:37:06+00:00
ID: 284
url: /index.php/datamgmt/datadesign/return-all-the-columns-in-the-database-w/
views:
  - 6104
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - database
  - information_schema
  - meta data
  - sql server

---
This question popped up on the MSDN forum today: [Status of BIT type columns][1]

Here is a way to quickly find all the columns in your database which allow NULLS

sql
select table_name,column_name
from information_schema.columns
where is_nullable ='YES'
order by table_name,column_name
```
Now you might have noticed that some of these are views. You can join with information\_schema.tables and filter on table\_type = &#8216;base table&#8217; to list just the tables.

sql
select c.table_name,c.column_name,t.table_type
from information_schema.columns c 
join information_schema.tables t on c.table_name = t.table_name
where is_nullable ='YES'
and table_type = 'base table'
order by table_name,column_name
```

To list all the columns in your database regardless if they are in a view or a table and if they allow NULLS or not use this

sql
select table_name,column_name,is_nullable
 from information_schema.columns
order by table_name,column_name
```
You can also find this in our [SQL Server Admin Hacks][2] wiki

\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][3] forum or our [Microsoft SQL Server Admin][4] forum**<ins></ins>

 [1]: http://social.msdn.microsoft.com/Forums/en-US/transactsql/thread/a30fcd83-7752-4b6e-9d23-6b351ba5e78e
 [2]: http://wiki.ltd.local/index.php/SQL_Server_Admin_Hacks
 [3]: http://forum.ltd.local/viewforum.php?f=17
 [4]: http://forum.ltd.local/viewforum.php?f=22