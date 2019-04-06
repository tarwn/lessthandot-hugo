---
title: Having Fun With OPENQUERY And Update,Delete And Insert Statements
author: SQLDenis
type: post
date: 2009-06-17T16:33:48+00:00
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

So let&#8217;s look at some code, first create a linked server on your machine that is linked to your machine. You can add a linked server through the wizard or through the sp_addlinkedserver stored proc

<pre>EXEC master.dbo.sp_addlinkedserver @server = N'localhost', @srvproduct=N'SQL Server'</pre>

Test that the linked server was created and that you can execute queries against it

<pre>select * from openquery(localhost,'select * from sysobjects')</pre>

Now that this is done we need to create a table and insert one row of data

<pre>use tempdb
go


create table test(id int primary key, col1 varchar(20))
go

insert test values(1,'test1')</pre>

Select from the table

<pre>select * from test</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;&#8212;-

<pre>id	col1
1	test1</pre>

**SELECT**
  
A select statement with openquery looks like this

<pre>select * from openquery(localhost,'select * from tempdb.dbo.test')</pre>

As you can see that was pretty standard

**INSERT**
  
an insert statement is a little odd looking because you still have the select in the openquery but the values that you want to insert look like a select statement that come after the openquery. It looks like this

<pre>INSERT  OPENQUERY(localhost,'SELECT id,col1 from tempdb.dbo.test ')
SELECT 2,'Test2'</pre>

Verify that the data is correct

<pre>select * from test</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;&#8212;-

<pre>id	col1
1	test1
2	Test2</pre>

**UPDATE**
  
The update has a select with a where clause inside the openquery statement and the set statement comes after that. Here is what this looks like

<pre>UPDATE OPENQUERY(localhost, 'select id,col1 from tempdb.dbo.test where id = 1 ')
SET id = 3,col1 = 'Test3'</pre>

Verify that the data is correct

<pre>select * from test</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;&#8212;-

<pre>id	col1
2	Test2
3	Test3</pre>

**DELETE**
  
Finally a delete will have the where clause inside openquery 

<pre>DELETE OPENQUERY(localhost, 'select id,col1 from tempdb.dbo.test where id = 2 ')</pre>

Verify that the data is correct

<pre>select * from test</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;&#8211;

<pre>id	col1
3	Test3</pre>

**When it doesn&#8217;t work**

I showed you what worked, now let&#8217;s look at some stuff that doesn&#8217;t
  
Create another table that looks like the table we had before but without a primary key

<pre>create table test2(id int, col1 varchar(20))
go

insert test2 values(1,'test1')</pre>

The select statement works without a problem

<pre>select * from openquery(localhost,'select * from tempdb.dbo.test2')</pre>

Output
  
&#8212;&#8212;&#8212;&#8212;&#8212;-

<pre>id	col1
1	test1</pre>

The insert statement also works

<pre>INSERT  OPENQUERY(localhost,'SELECT id,col1 from tempdb.dbo.test2 ')
SELECT 2,'test2'</pre>

Verify that the data is correct

<pre>select * from test2</pre>

<pre>1	test1
2	test2</pre>

If you run the following update query

<pre>UPDATE OPENQUERY(localhost, 'select id,col1 from tempdb.dbo.test2 where id = 1 ')
SET id = 3,col1 = 'test2'</pre>

you will be greeted with this message

_Server: Msg 7320, Level 16, State 2, Line 1
  
Could not execute query against OLE DB provider &#8216;SQLOLEDB&#8217;. The provider could not support a required row lookup interface. The provider indicates that conflicts occurred with other properties or requirements.
  
[OLE/DB provider returned message: Multiple-step OLE DB operation generated errors. Check each OLE DB status value, if available. No work was done.]
  
OLE DB error trace [OLE/DB Provider &#8216;SQLOLEDB&#8217; ICommandText::Execute returned 0x80040e21: select id,col1 from tempdb.dbo.test2 where id = 1 [PROPID=DBPROP\_IRowsetLocate VALUE=True STATUS=DBPROPSTATUS\_CONFLICTING], [PROPID=DBPROP\_BOOKMARKS VALUE=True STATUS=DBPROPSTATUS\_CONFLICTING]]._

The delete has the same issue as the update statement, running the following

<pre>DELETE OPENQUERY(localhost, 'select id,col1 from tempdb.dbo.test2 where id = 2 ')</pre>

will produce this error message

_Server: Msg 7320, Level 16, State 2, Line 1
  
Could not execute query against OLE DB provider &#8216;SQLOLEDB&#8217;. The provider could not support a required row lookup interface. The provider indicates that conflicts occurred with other properties or requirements.
  
[OLE/DB provider returned message: Multiple-step OLE DB operation generated errors. Check each OLE DB status value, if available. No work was done.]
  
OLE DB error trace [OLE/DB Provider &#8216;SQLOLEDB&#8217; ICommandText::Execute returned 0x80040e21: select id,col1 from tempdb.dbo.test2 where id = 2 [PROPID=DBPROP\_IRowsetLocate VALUE=True STATUS=DBPROPSTATUS\_CONFLICTING], [PROPID=DBPROP\_BOOKMARKS VALUE=True STATUS=DBPROPSTATUS\_CONFLICTING]]._

So what those errors are telling us is that a row lookup could not be performed, this is of course because we don&#8217;t have a key on this table. So just be aware of that!

Now that we are done we can drop the linked server, you can use sp_dropserver to do that

<pre>sp_dropserver 'localhost'</pre>



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.ltd.local/viewforum.php?f=17
 [2]: http://forum.ltd.local/viewforum.php?f=22