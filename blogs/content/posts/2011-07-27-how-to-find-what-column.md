---
title: How to find what column caused the String or binary data would be truncated message
author: SQLDenis
type: post
date: 2011-07-27T15:39:00+00:00
ID: 1265
excerpt: How to find what column caused the String or binary data would be truncated message. Sometimes you get data from Excel or another system and you need to import that data into your tables. Some people will use an import wizard and all the columns will be nvarchar(255) in the import table
url: /index.php/datamgmt/datadesign/how-to-find-what-column/
views:
  - 75730
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - errors
  - importing
  - truncation

---
Sometimes you get data from Excel or another system and you need to import that data into your tables. Some people will use an import wizard and all the columns will be nvarchar(255) in the import table

Here is an example of a table created by the import/export wizard based on an Excel file

sql
CREATE TABLE [dbo].[Sheet1$] (
[emailaddress] nvarchar(255),
[Value] nvarchar(255),
[CategoryName] nvarchar(255),
[CategoryDescription] nvarchar(255),
[CategoryID] float,
[UserAdditionalInfoTextValue] nvarchar(255),
[F9] nvarchar(255)
)
```

Sometimes the data that you are importing won't fit into your column and you get the helpful **String or binary data would be truncated** message.
  
Of course it doesn't tell you which column it is, now you need to figure out which column and compare the tables

I will show you what I mean, first create these two tables

sql
use tempdb
go

create table TestTrunc(
	Col1 varchar(10),
	Col2 varchar(15),
	Col3 varchar(20),
	Col4 nchar(3),
	Col5 nvarchar(10))
	Go
	
	
	create table temp(
	Col1 varchar(50),
	Col2 varchar(50),
	Col3 varchar(50),
	Col4 nchar(10),
	Col5 nvarchar(50))		
	GO
```

Now let's insert some data into the temp table 

sql
insert 	temp
select '1234567890','12345678901234567890','bla','1234','123456789011111'	
union all
select '1234567890111','1233','bla111','1234','123456111'	
```

If we now try to insert the data from the temp table into the TestTrunc table it will blow up

sql
insert TestTrunc
select * from temp
```

_Msg 8152, Level 16, State 14, Line 1
  
String or binary data would be truncated.
  
The statement has been terminated._

Yes, very nice….could you indicate which column it actually has a problem with?

Now the purpose of this post is to create code that you then can modify for your own needs and can use it to compare any two tables.

First thing we need to know is what the columns are in the table that are (n)chars or (n)varchar

sql
select column_name
from information_schema.columns
where table_name = 'temp'
and data_type in('varchar','char','nvarchar','nchar')
```

column_name
  
————–
  
Col1
  
Col2
  
Col3
  
Col4
  
Col5

That was easy, now we want to know the max length of the data in each column

sql
declare @sql varchar(8000)
select @sql = 'select  0 as _col0 ,'
select @sql +=   'max(len( ' + column_name+ ')) AS ' + column_name + ',' 
from information_schema.columns
where table_name = 'temp'
and data_type in('varchar','char','nvarchar','nchar')

select @sql = left(@sql,len(@sql) -1)
select @sql +=' into MaxLengths from temp'

--select @sql -debugging so simple, a caveman can do it

exec (@sql)
```

That code basically creates and runs the following 

sql
select  0 as _col0 ,
	max(len( Col1)) AS Col1,
	max(len( Col2)) AS Col2,
	max(len( Col3)) AS Col3,
	max(len( Col4)) AS Col4,
	max(len( Col5)) AS Col5 
into MaxLengths 
from temp
```

If we now look in the MaxLengths table we will see the following

sql
select * from MaxLengths
```



<pre>_col0	Col1	Col2	Col3	Col4	Col5
---------------------------------------------------
0	13	20	6	4	15</pre>

Next to figure out is what the max length of the column itself is in the table that we want to insert into
  
Run the following query

sql
select character_maximum_length,column_name
from information_schema.columns
where table_name = 'TestTrunc'
and data_type in('varchar','char','nvarchar','nchar')
```

Here is the result
  


<pre>character_maximum_length	column_name
--------------------------------------------
10				Col1
15				Col2
20				Col3
3				Col4
10				Col5</pre>

We will again do this dynamically and insert the values into another table

sql
declare @sql varchar(8000)
select @sql = 'select 0 as _col0, '
select @sql +=   '' + convert(varchar(20),character_maximum_length)+ ' AS ' + column_name + ',' 
from information_schema.columns
where table_name = 'TestTrunc'
and data_type in('varchar','char','nvarchar','nchar')

select @sql = left(@sql,len(@sql) -1)
select @sql +=' into TempTrunc '

--select @sql -debugging so simple, a caveman can do it

exec (@sql)
```

Now we can see what we have in the two tables

sql
select 'TempTrunc' as TableNAme,* from TempTrunc
union all
select 'MaxLengths' as TableNAme,* from MaxLengths
```



<pre>TableNAme	_col0	Col1	Col2	Col3	Col4	Col5
-------------------------------------------------------------
TempTrunc	0	10	15	20	3	10
MaxLengths	0	13	20	6	4	15</pre>

As you can see, all columns except for Col3 will cause the truncation problem
  
Of course we want to do something like this, it will tell us which columns have truncation problems

sql
select  case when  t.col1 > tt.col1 then 'truncation' else 'no truncation' end as Col1,
 case when  t.col2 > tt.col2 then 'truncation' else 'no truncation' end as Col2,
 case when  t.col3 > tt.col3 then 'truncation' else 'no truncation'  end as Col3,
 case when  t.col4 > tt.col4 then 'truncation'  else 'no truncation' end as Col4,
 case when  t.col5 > tt.col5 then 'truncation' else 'no truncation'  end as Col5
   from MaxLengths t
join TempTrunc tt on t._col0 = tt._col0
```



<pre>Col1		Col2		Col3			Col4		Col5
------------------------------------------------------------------------------------
truncation	truncation	no truncation	     truncation	     truncation</pre>

And again, we will use a dynamic approach, we don't know the real column names

sql
declare @sql varchar(8000)
select @sql = 'select  '
select @sql +=   '' + 'case when  t.' + column_name + ' > tt.' + column_name
+ ' then ''truncation'' else ''no truncation'' end as '+ column_name
+ ',' 
from information_schema.columns
where table_name = 'MaxLengths'
and column_name <> '_col0'
select @sql = left(@sql,len(@sql) -1)
select @sql +='  from MaxLengths t
join TempTrunc tt on t._col0 = tt._col0 '

--select @sql -debugging so simple, a caveman can do it

exec (@sql)
```


<pre>Col1		Col2		Col3			Col4		Col5
------------------------------------------------------------------------------------
truncation	truncation	no truncation	      truncation    truncation</pre>

That is all there is to it, you can of course customize it by passing in the table names and make this part of your toolkit to quickly see where the problem is

## Making it truly dynamic

Here is what it would look like with dynamic table names

sql
declare @ImportTable varchar(100)
declare @DestinationTable varchar(100)
select @ImportTable = 'temp'
select @DestinationTable = 'TestTrunc'

declare @ImportTableCompare varchar(100)
declare @DestinationTableCompare varchar(100)
select @ImportTableCompare = 'MaxLengths'
select @DestinationTableCompare = 'TempTrunc'


declare @sql varchar(8000)
select @sql  = ''
select @sql = 'select  0 as _col0 ,'
select @sql +=   'max(len( ' + column_name+ ')) AS ' + column_name + ',' 
from information_schema.columns
where table_name = @ImportTable
and data_type in('varchar','char','nvarchar','nchar')

select @sql = left(@sql,len(@sql) -1)
select @sql +=' into ' + @ImportTableCompare + ' from ' + @ImportTable

--select @sql -debugging so simple, a caveman can do it

exec (@sql)



select @sql  = ''
select @sql = 'select 0 as _col0, '
select @sql +=   '' + convert(varchar(20),character_maximum_length)+ ' AS ' + column_name + ',' 
from information_schema.columns
where table_name = @DestinationTable
and data_type in('varchar','char','nvarchar','nchar')

select @sql = left(@sql,len(@sql) -1)
select @sql +=' into ' + @DestinationTableCompare

--select @sql -debugging so simple, a caveman can do it

exec (@sql)


select @sql  = ''
select @sql = 'select  '
select @sql +=   '' + 'case when  t.' + column_name + ' > tt.' + column_name
+ ' then ''truncation'' else ''no truncation'' end as '+ column_name
+ ',' 
from information_schema.columns
where table_name = @ImportTableCompare
and column_name <> '_col0'
select @sql = left(@sql,len(@sql) -1)
select @sql +='  from ' + @ImportTableCompare + ' t
join ' + @DestinationTableCompare + ' tt on t._col0 = tt._col0 '

--select @sql -debugging so simple, a caveman can do it

exec (@sql)


exec ('drop table ' + @ImportTableCompare+ ',' + @DestinationTableCompare )
```


### To Do list

Here are some things for you to add…….

Now if you have worked with SQL Server for a while now, you might notice that what I did was not really what you would call defensive programming.
  
Can you tell what I forgot?

**1)** if the column name has spaces or other weird characters I am not taking care of that. To take care of that use the QUOTENAME function or put brackets around column_name

**2)** I also didn't look at the schema the table is in, you can use the TABLE_SCHEMA column for that, and the same applies as for columns, use QUOTENAME to take care of schemas that have an invalid name.

**3)** LEN trims spaces, be aware of that otherwise you might still get truncation if inserting into a char column.

example

sql
create table TestTrim(SomeData char(3))
go

declare @d char(5) = '12  '
select len(@d) as len ,datalength(@d) as datalength

--len	datalength
--2		5


insert TestTrim
values (@d)

select *,len(SomeData) as len,datalength(SomeData)  as datalength
from TestTrim
--SomeData	len		datalength
--12 		2		3
```
**4)** The columns that have varchar(max) values
  
Create this table
  
create table TestTrim(SomeData varchar(max))
  
go

Now run this and observe character\_maximum\_length
  
select * from information_schema.columns
  
where table_name = 'TestTrim'

As you can see the character\_maximum\_length is -1 for (max) columns, you need to take that into account

**5)** If your database uses a case sensitive collation then you need to take that into account with column and table names

That is it for this post, hopefully the code helps you to find out where the truncation occurs and if you do the 5 modifications like I suggested it should be pretty bullet proof

Bonus, use IIF instead of CASE.
  
If you are using SQL Server Denali CTP3 or up, you can use IIF instead of CASE. See [A Quick look at the new IIF function in Denali CTP3 for more information about IIF][1]

sql
select  
case when  t.col1 > tt.col1 then 'truncation' else 'no truncation' end as Col1,
case when  t.col2 > tt.col2 then 'truncation' else 'no truncation' end as Col2,
case when  t.col3 > tt.col3 then 'truncation' else 'no truncation'  end as Col3,
case when  t.col4 > tt.col4 then 'truncation' else 'no truncation' end as Col4,
case when  t.col5 > tt.col5 then 'truncation' else 'no truncation'  end as Col5
from MaxLengths t
join TempTrunc tt on t._col0 = tt._col0
```

sql
select  
IIF(t.col1 > tt.col1,'truncation','no truncation') as Col1,
IIF(t.col2 > tt.col2,'truncation','no truncation') as Col2,
IIF(t.col3 > tt.col3,'truncation','no truncation') as Col3,
IIF(t.col4 > tt.col4,'truncation','no truncation') as Col4,
IIF(t.col5 > tt.col5,'truncation','no truncation') as Col5
from MaxLengths t
join TempTrunc tt on t._col0 = tt._col0
```


 [1]: /index.php/DataMgmt/DBProgramming/MSSQLServer/a-quick-look-at-the