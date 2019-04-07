---
title: Use WITH RESULT SETS to change column names and datatypes of a resultset
author: SQLDenis
type: post
date: 2013-03-17T21:59:00+00:00
ID: 2036
excerpt: |
  SQL Server 2012 has added WITH RESULT SETS to the EXCECUTE command. You can now override the data types and names that the resultset is returning
  
  EXEC ('SELECT name FROM sys.tables' )
  WITH RESULT SETS
  ( 
     (TableName nvarchar(100))
  );
  
  
  Table&hellip;
url: /index.php/datamgmt/dbprogramming/use-with-result-sets-to/
views:
  - 9012
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - how to
  - sql server 2012

---
SQL Server 2012 has added WITH RESULT SETS to the EXCECUTE command. You can now override the data types and names that the resultset is returning
  
Here is one example where we change the name of the column from _name_ to _TableName_

sql
EXEC ('SELECT name FROM sys.tables' )
WITH RESULT SETS
( 
   (TableName nvarchar(100))
);
```

<pre>TableName
-----------
DimAccount
DimCurrency
DimCustomer
DimDate
DimDepartmentGroup</pre>

Of course we could have just aliased it as well instead.
  
You can also changed the datatype, here is a silly example

sql
EXEC ('SELECT object_id FROM sys.tables' )
WITH RESULT SETS
( 
   (ObjectID decimal(20,2))
);
```
Here is the output

<pre>ObjectID
-----------
5575058.00
21575115.00
37575172.00
53575229.00</pre>

So far we could have accomplished everything we did by using an alias or cast/convert. However, you can also use WITH RESULT SETS when executing stored procedures. Now you might be saying that it is no big deal since you can change the stored procedure. What about a system stored procedure, can/would you change that?
  
Take sp_helpdb for example. If you execute the following

sql
EXEC sp_helpdb
```

You will get the following columns

<pre>name	             db_size	owner	      dbid	created	        status    compatibility_level
AdventureWorks2012    189.49 MB	DenisDenis	16	Mar 16 2013	Status=ONLINE, .....	110
AdventureWorksDW2008R2 92.56 MB	DenisDenis	15	Mar 16 2013	Status=ONLINE.....	100
AdventureWorksDW2012  201.74 MB	DenisDenis	14	Mar 16 2013	Status=ONLINE.....	110</pre>

Let&#8217;s say we want to change the names of all the columns and also we want to make the column created return a datetime value. You can of course dump the stored procedure output in a temporary table and select from that table. But in this case I think WITH RESULT SETS shows its value. Here is how we do this with sp_helpdb

sql
EXEC sp_helpdb
WITH RESULT SETS
( 
   (
   DatabaseName nvarchar(100),
   DatabaseSize nvarchar(1000),
   DatabaseOwner nvarchar(100),
   DatabaseID int,
   DateCreated datetime,
   DatabaseStatus nvarchar(2000),
   DatabaseCompatabilitylevel int 
   )
);
```

Here is what the output looks like

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/sp_helpdb.PNG?mtime=1363564008"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/SQL2013/sp_helpdb.PNG?mtime=1363564008" width="822" height="253" /></a>
</div>

As you can see the column names are what we specified and DateCreated is now a datetime, instead of Mar 16 2013 we now see 2013-03-16 00:00:00.000. I think WITH RESULT SETS is a nice addition to the product, especially if you have to deal with stored procedure created by third party vendors or system procs