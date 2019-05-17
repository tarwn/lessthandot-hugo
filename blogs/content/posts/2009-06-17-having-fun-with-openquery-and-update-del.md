---
title: Having Fun With OPENQUERY And Update,Delete And Insert Statements
author: SQLDenis
type: post
date: 2009-06-17T16:33:48+00:00
ID: 475
url: /index.php/datamgmt/datadesign/having-fun-with-openquery-and-update-del/
views:
  - 44691
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - how to
  - linked server
  - openquery
  - sql server
  - sybase

---
Recently I had to do some data manipulation against a Sybase linked server running on a Unix box. When you have a linked server you can use OPENQUERY do perform DML statements like delete, update and inserts but the syntax is a little different

So let's look at some code, first create a linked server on your machine that is linked to your machine. You can add a linked server through the wizard or through the sp_addlinkedserver stored proc

```sql
EXEC master.dbo.sp_addlinkedserver @server = N'localhost', @srvproduct=N'SQL Server'
```

Test that the linked server was created and that you can execute queries against it

```sql
select * from openquery(localhost,'select * from sysobjects')
```

Now that this is done we need to create a table and insert one row of data

```sql
use tempdb
go


create table test(id int primary key, col1 varchar(20))
go

insert test values(1,'test1')
```

Select from the table

```sql
select * from test
```

Output
  
—————-

<pre>id	col1
1	test1</pre>

**SELECT**
  
A select statement with openquery looks like this

```sql
select * from openquery(localhost,'select * from tempdb.dbo.test')
```

As you can see that was pretty standard

**INSERT**
  
an insert statement is a little odd looking because you still have the select in the openquery but the values that you want to insert look like a select statement that come after the openquery. It looks like this

```sql
INSERT  OPENQUERY(localhost,'SELECT id,col1 from tempdb.dbo.test ')
SELECT 2,'Test2'
```

Verify that the data is correct

```sql
select * from test
```

Output
  
—————-

<pre>id	col1
1	test1
2	Test2</pre>

**UPDATE**
  
The update has a select with a where clause inside the openquery statement and the set statement comes after that. Here is what this looks like

```sql
UPDATE OPENQUERY(localhost, 'select id,col1 from tempdb.dbo.test where id = 1 ')
SET id = 3,col1 = 'Test3'
```

Verify that the data is correct

```sql
select * from test
```

Output
  
—————-

<pre>id	col1
2	Test2
3	Test3</pre>

**DELETE**
  
Finally a delete will have the where clause inside openquery 

```sql
DELETE OPENQUERY(localhost, 'select id,col1 from tempdb.dbo.test where id = 2 ')
```

Verify that the data is correct

```sql
select * from test
```

Output
  
————–

<pre>id	col1
3	Test3</pre>

**When it doesn't work**

I showed you what worked, now let's look at some stuff that doesn't
  
Create another table that looks like the table we had before but without a primary key

```sql
create table test2(id int, col1 varchar(20))
go

insert test2 values(1,'test1')
```

The select statement works without a problem

```sql
select * from openquery(localhost,'select * from tempdb.dbo.test2')
```

Output
  
—————-

<pre>id	col1
1	test1</pre>

The insert statement also works

```sql
INSERT  OPENQUERY(localhost,'SELECT id,col1 from tempdb.dbo.test2 ')
SELECT 2,'test2'
```

Verify that the data is correct

```sql
select * from test2
```

<pre>1	test1
2	test2</pre>

If you run the following update query

```sql
UPDATE OPENQUERY(localhost, 'select id,col1 from tempdb.dbo.test2 where id = 1 ')
SET id = 3,col1 = 'test2'
```

you will be greeted with this message

_Server: Msg 7320, Level 16, State 2, Line 1
  
Could not execute query against OLE DB provider 'SQLOLEDB'. The provider could not support a required row lookup interface. The provider indicates that conflicts occurred with other properties or requirements.
  
[OLE/DB provider returned message: Multiple-step OLE DB operation generated errors. Check each OLE DB status value, if available. No work was done.]
  
OLE DB error trace [OLE/DB Provider 'SQLOLEDB' ICommandText::Execute returned 0x80040e21: select id,col1 from tempdb.dbo.test2 where id = 1 [PROPID=DBPROP\_IRowsetLocate VALUE=True STATUS=DBPROPSTATUS\_CONFLICTING], [PROPID=DBPROP\_BOOKMARKS VALUE=True STATUS=DBPROPSTATUS\_CONFLICTING]]._

The delete has the same issue as the update statement, running the following

```sql
DELETE OPENQUERY(localhost, 'select id,col1 from tempdb.dbo.test2 where id = 2 ')
```
will produce this error message

_Server: Msg 7320, Level 16, State 2, Line 1
  
Could not execute query against OLE DB provider 'SQLOLEDB'. The provider could not support a required row lookup interface. The provider indicates that conflicts occurred with other properties or requirements.
  
[OLE/DB provider returned message: Multiple-step OLE DB operation generated errors. Check each OLE DB status value, if available. No work was done.]
  
OLE DB error trace [OLE/DB Provider 'SQLOLEDB' ICommandText::Execute returned 0x80040e21: select id,col1 from tempdb.dbo.test2 where id = 2 [PROPID=DBPROP\_IRowsetLocate VALUE=True STATUS=DBPROPSTATUS\_CONFLICTING], [PROPID=DBPROP\_BOOKMARKS VALUE=True STATUS=DBPROPSTATUS\_CONFLICTING]]._

So what those errors are telling us is that a row lookup could not be performed, this is of course because we don't have a key on this table. So just be aware of that!

Now that we are done we can drop the linked server, you can use sp_dropserver to do that

```sql
sp_dropserver 'localhost'
```



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22