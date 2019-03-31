---
title: Use alter table alter column to change datatypes for a column in SQL Server
author: SQLDenis
type: post
date: 2010-01-19T16:10:48+00:00
url: /index.php/datamgmt/datadesign/use-alter-table-alter-column-to-change-d/
views:
  - 35351
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
  - design
  - how to
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tables

---
This question popped in the [microsoft.public.sqlserver.programming][1] forum yesterday. A person wanted to change a column from varchar 500 to varchar 2000. This is actually pretty easy to do in SQL Server, you can use the _alter table alter column_ command
  
Let&#8217;s take a quick look at how this works
  
First create this table

<pre>CREATE TABLE [TestTable] (
[id] [int] IDENTITY (1, 1) NOT NULL ,
[itemdate] [datetime] NOT NULL ,
[title] [varchar] (500) NOT NULL ,
) ON [PRIMARY]</pre>

Now insert one row of data

<pre>insert [TestTable]
select getdate(),'bla'</pre>

Now do a simple select and verify that you have one row of data

<pre>select * from [TestTable]</pre>

Results

<pre>id          itemdate                title
----------- ----------------------- ----------
1           2010-01-18 17:28:19.820 bla</pre>

We will change the column from varchar 500 to varchar 2000. All you have to do is alter table \[table name] alter column [column name\] \[new data type and length\]
  
So in this case the command would look like this

<pre>alter table [TestTable] alter column [title] [varchar] (2000)</pre>

To verify that indeed the column is now varchar 2000 run the following query

<pre>select COLUMN_NAME,DATA_TYPE,CHARACTER_MAXIMUM_LENGTH
from INFORMATION_SCHEMA.COLUMNS
where table_name = 'TestTable'</pre>

Results

<pre>COLUMN_NAME    DATA_TYPE       CHARACTER_MAXIMUM_LENGTH
-------------- -----------------------------------------
id              int             NULL
itemdate        datetime        NULL
title           varchar         2000</pre>

Now let&#8217;s change the datetime column to a varchar, execute the following query

<pre>alter table [TestTable] alter column [itemdate] [varchar] (49)</pre>

Now verify that it was changed

<pre>select COLUMN_NAME,DATA_TYPE,CHARACTER_MAXIMUM_LENGTH
from INFORMATION_SCHEMA.COLUMNS
where table_name = 'TestTable'</pre>

Results

<pre>COLUMN_NAME    DATA_TYPE       CHARACTER_MAXIMUM_LENGTH
-------------- -----------------------------------------
id              int             NULL
itemdate        varchar         49
title           varchar         2000</pre>

Insert a new row

<pre>insert [TestTable]
select getdate(),'bla'</pre>

Now check what is in the table

<pre>select * from [TestTable]</pre>

Results

<pre>id          itemdate                                          title
----------- ------------------------------------------------- -----------
1           Jan 18 2010  5:28PM                               bla
2           Jan 18 2010  5:30PM                               bla</pre>

Now we will add a column. The command to add a column is very similar to the one where you alter a column, the difference is that you don&#8217;t use the column keyword. Below is a query that will add an int column with a default of 0

<pre>alter table [TestTable] add  [SomeInt] int default 0 not null</pre>

Run this query to see what is in the table now

<pre>select * from [TestTable]</pre>

Results

<pre>id          itemdate                    title     SomeInt
----------- ---------------------------- ---------- -----------
1           Jan 18 2010  5:28PM         bla       0
2           Jan 18 2010  5:30PM         bla       0</pre>

As you can see the column was added and the default of 0 was used.

We can interrogate the INFORMATION_SCHEMA.COLUMNS view again to verify that the column is there

<pre>select COLUMN_NAME,DATA_TYPE,CHARACTER_MAXIMUM_LENGTH
from INFORMATION_SCHEMA.COLUMNS
where table_name = 'TestTable'</pre>

Results

<pre>COLUMN_NAME    DATA_TYPE       CHARACTER_MAXIMUM_LENGTH
-------------- -----------------------------------------
id              int             NULL
itemdate        varchar         49
title           varchar         2000
SomeInt		int		NULL</pre>

Now let&#8217;s insert a row, we will use a value of 2 for the newly added SomeInt column

<pre>insert [TestTable]
select getdate(),'bla',2</pre>

When we run this query again

<pre>select * from [TestTable]</pre>

We can see that the value 2 is there

Results

<pre>id          itemdate			title     SomeInt
----------- ------------------------------------------------- 
1           Jan 18 2010  5:28PM           bla       0
2           Jan 18 2010  5:30PM           bla       0
3           Jan 18 2010  5:33PM           bla       2</pre>

If you try to change a column to a datatype which is incompatible with the data that is stored you will get an error message

<pre>alter table [TestTable] alter column [itemdate] int</pre>

Here is the error for the above command
  
**Msg 245, Level 16, State 1, Line 1
  
Conversion failed when converting the varchar value &#8216;Jan 18 2010 5:12PM&#8217; to data type int.
  
The statement has been terminated.**

That is it for this post, as you can see it is pretty easy to change the data type of a column by using the alter table alter column command

\*** **Remember, if you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://groups.google.com/group/microsoft.public.sqlserver.programming/browse_thread/thread/91c5da9982cfb1cf?hl=en#
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22