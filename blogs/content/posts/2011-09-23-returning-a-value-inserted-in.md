---
title: Returning a value inserted in a table with a newsequentialid() default on a uniqueidentifier column
author: SQLDenis
type: post
date: 2011-09-23T15:47:00+00:00
ID: 1326
excerpt: 'This question came up yesterday and I decided to do a little blog post about it. Someone wanted to know if there was something like @@identity/scope_identity() for a uniqueidentifier column with a default of newsequentialid(). There is not such a functi&hellip;'
url: /index.php/datamgmt/datadesign/returning-a-value-inserted-in/
views:
  - 6535
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - database
  - how to
  - sql server 2005
  - sql server 2008
  - uniqueidentifier

---
This [question][1] came up yesterday and I decided to do a little blog post about it. Someone wanted to know if there was something like @@identity/scope_identity() for a uniqueidentifier column with a default of newsequentialid(). There is not such a function but you can use OUTPUT INSERTED.Column to do something similar. Let's take a look

First create this table

sql
USE tempdb
GO
CREATE TABLE bla(ID INT,SomeID UNIQUEIDENTIFIER DEFAULT newsequentialid())


INSERT bla (ID) VALUES(1)
GO
```

Do a simple select....

sql
SELECT * FROM bla
```

As you can see we have 1 row

<pre>ID	SomeID
1	D6911D8A-0AE6-E011-A428-0021867E1D41</pre>

Here is what the insert looks like that also returns the uniqueidentifier just created by the newsequentialid()default

sql
INSERT bla (ID)
    OUTPUT INSERTED.SomeID
VALUES(2)
```



<pre>SomeID
28E798A8-0AE6-E011-A428-0021867E1D41</pre>

As you can see you get the uniqueidentifier just created back, all we have added was OUTPUT INSERTED.SomeID between INSERT..... and VALUES......
  
Pretty simple so far
  
Now, we should have two rows in the table

sql
SELECT * FROM bla
```



<pre>ID	SomeID
1	D6911D8A-0AE6-E011-A428-0021867E1D41
2	28E798A8-0AE6-E011-A428-0021867E1D41</pre>

You can also populate a table variable and then use that to return the values

sql
DECLARE @MyTableVar TABLE( SomeID UNIQUEIDENTIFIER)
INSERT bla (ID)
    OUTPUT INSERTED.SomeID
        INTO @MyTableVar
VALUES(3)
 
SELECT SomeID FROM @MyTableVar
```



<pre>SomeID
D26351C1-0AE6-E011-A428-0021867E1D41</pre>

Finally we can run a select that confirms all 3 inserts actually have happened

sql
SELECT * FROM bla
```

Here is the data, of course on your machine the values for SomeID won't be the same
  


<pre>ID	SomeID
1	D6911D8A-0AE6-E011-A428-0021867E1D41
2	28E798A8-0AE6-E011-A428-0021867E1D41
3	D26351C1-0AE6-E011-A428-0021867E1D41</pre>

That is it for this post, for some more OUTPUT examples take a look at [Using T-SQL OUTPUT and MERGE To Link Old and New Keys][2]

 [1]: http://forum.ltd.local/viewtopic.php?f=17&t=15344
 [2]: /index.php/DataMgmt/DBProgramming/MSSQLServer/using-t-sql-output-and-merge