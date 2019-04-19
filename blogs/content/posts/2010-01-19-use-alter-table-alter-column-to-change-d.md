---
title: Use alter table alter column to change datatypes for a column in SQL Server
author: SQLDenis
type: post
date: 2010-01-19T16:10:48+00:00
ID: 681
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
  
Let's take a quick look at how this works
  
First create this table

```sql
CREATE TABLE [TestTable] (
[id] [int] IDENTITY (1, 1) NOT NULL ,
[itemdate] [datetime] NOT NULL ,
[title] [varchar] (500) NOT NULL ,
) ON [PRIMARY]
```

Now insert one row of data

```sql
insert [TestTable]
select getdate(),'bla'
```

Now do a simple select and verify that you have one row of data

```sql
select * from [TestTable]
```

Results

<pre>id          itemdate                title
----------- ----------------------- ----------
1           2010-01-18 17:28:19.820 bla</pre>

We will change the column from varchar 500 to varchar 2000. All you have to do is alter table \[table name] alter column [column name\] \[new data type and length\]
  
So in this case the command would look like this

```sql
alter table [TestTable] alter column [title] [varchar] (2000)
```

To verify that indeed the column is now varchar 2000 run the following query

```sql
select COLUMN_NAME,DATA_TYPE,CHARACTER_MAXIMUM_LENGTH
from INFORMATION_SCHEMA.COLUMNS
where table_name = 'TestTable'
```

Results

<pre>COLUMN_NAME    DATA_TYPE       CHARACTER_MAXIMUM_LENGTH
-------------- -----------------------------------------
id              int             NULL
itemdate        datetime        NULL
title           varchar         2000</pre>

Now let's change the datetime column to a varchar, execute the following query

```sql
alter table [TestTable] alter column [itemdate] [varchar] (49)
```

Now verify that it was changed

```sql
select COLUMN_NAME,DATA_TYPE,CHARACTER_MAXIMUM_LENGTH
from INFORMATION_SCHEMA.COLUMNS
where table_name = 'TestTable'
```

Results

<pre>COLUMN_NAME    DATA_TYPE       CHARACTER_MAXIMUM_LENGTH
-------------- -----------------------------------------
id              int             NULL
itemdate        varchar         49
title           varchar         2000</pre>

Insert a new row

```sql
insert [TestTable]
select getdate(),'bla'
```

Now check what is in the table

```sql
select * from [TestTable]
```

Results

<pre>id          itemdate                                          title
----------- ------------------------------------------------- -----------
1           Jan 18 2010  5:28PM                               bla
2           Jan 18 2010  5:30PM                               bla</pre>

Now we will add a column. The command to add a column is very similar to the one where you alter a column, the difference is that you don't use the column keyword. Below is a query that will add an int column with a default of 0

```sql
alter table [TestTable] add  [SomeInt] int default 0 not null
```

Run this query to see what is in the table now

```sql
select * from [TestTable]
```

Results

<pre>id          itemdate                    title     SomeInt
----------- ---------------------------- ---------- -----------
1           Jan 18 2010  5:28PM         bla       0
2           Jan 18 2010  5:30PM         bla       0</pre>

As you can see the column was added and the default of 0 was used.

We can interrogate the INFORMATION_SCHEMA.COLUMNS view again to verify that the column is there

```sql
select COLUMN_NAME,DATA_TYPE,CHARACTER_MAXIMUM_LENGTH
from INFORMATION_SCHEMA.COLUMNS
where table_name = 'TestTable'
```

Results

<pre>COLUMN_NAME    DATA_TYPE       CHARACTER_MAXIMUM_LENGTH
-------------- -----------------------------------------
id              int             NULL
itemdate        varchar         49
title           varchar         2000
SomeInt		int		NULL</pre>

Now let's insert a row, we will use a value of 2 for the newly added SomeInt column

```sql
insert [TestTable]
select getdate(),'bla',2
```

When we run this query again

```sql
select * from [TestTable]
```

We can see that the value 2 is there

Results

<pre>id          itemdate			title     SomeInt
----------- ------------------------------------------------- 
1           Jan 18 2010  5:28PM           bla       0
2           Jan 18 2010  5:30PM           bla       0
3           Jan 18 2010  5:33PM           bla       2</pre>

If you try to change a column to a datatype which is incompatible with the data that is stored you will get an error message

```sql
alter table [TestTable] alter column [itemdate] int
```

Here is the error for the above command
  
**Msg 245, Level 16, State 1, Line 1
  
Conversion failed when converting the varchar value 'Jan 18 2010 5:12PM' to data type int.
  
The statement has been terminated.**

That is it for this post, as you can see it is pretty easy to change the data type of a column by using the alter table alter column command

\*** **Remember, if you have a SQL related question try our [Microsoft SQL Server Programming][2] forum or our [Microsoft SQL Server Admin][3] forum**<ins></ins>

 [1]: http://groups.google.com/group/microsoft.public.sqlserver.programming/browse_thread/thread/91c5da9982cfb1cf?hl=en#
 [2]: http://forum.ltd.local/viewforum.php?f=17
 [3]: http://forum.ltd.local/viewforum.php?f=22