---
title: Listing All The User-Defined Data Types That Were Created On Your SQL Server
author: SQLDenis
type: post
date: 2009-06-16T15:25:14+00:00
url: /index.php/datamgmt/datadesign/listing-all-the-user-defined-data-types/
views:
  - 10194
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - howto
  - sql server 2000
  - sql server 2005
  - sql server 2008

---
If you have a bunch of User-Defined Data Types in your databases and you would like to get a list of them then you can run the following query

On SQL Server 2000 and up

<pre>select * from systypes
where xusertype &gt; 256</pre>

or

On SQL Server 2005 and up

<pre>SELECT * FROM sys.Types 
WHERE is_user_defined = 1</pre>

Let&#8217;s take a look how this works by adding a couple of User-Defined Data Types. we will add a birthday type which will be a datetime (on SQL Server 2008 it should be a date) and a StateCode which is a char(2)

<pre>USE master
EXEC sp_addtype birthday, datetime, 'NULL'
Go
EXEC sp_addtype StateCode,'Char(2)' , 'NULL'
Go</pre>

Now we can create a table which uses these two types

<pre>create table TestType (Birthday birthday,State StateCode)</pre>

Insert some data

<pre>insert TestType values('19100101','NY')
insert TestType values('19800101','CA')</pre>

And we can query the data as usual

<pre>select * from TestType</pre>

To see what data type is actually used to store the data we can run the following query

<pre>select column_name,data_type,character_maximum_length
from information_schema.columns
where table_name = 'TestType'</pre>

output

<pre>-----------------
column_name	data_type	character_maximum_length
Birthday	datetime	
State		char		2</pre>



As you can see datetime and char(2) are used

We can also use the SysTypes (SQL Server 2000 and up) and Sys.Types system tables/catalog views

<pre>SELECT s.name,s2.name,s2.length
 FROM SysTypes s
join SysTypes s2 on s.xtype = s2.xtype
WHERE s.xusertype &gt; 256
and s2.xusertype &lt;= 256</pre>

output

<pre>----------------------
name		name		length
birthday	datetime	8
StateCode	char		8000</pre>

<pre>SELECT s.name,s2.name,s2.max_length
FROM Sys.Types s
join Sys.Types s2 on s2.user_type_id = s.system_type_id
WHERE s.is_user_defined = 1
and s2.is_user_defined = 0</pre>

output

<pre>----------------------
name		name		length
birthday	datetime	8
StateCode	char		8000</pre>

How do you drop a User-Defined Data Type?
  
Here is how you do it. Run the following query

<pre>USE master
EXEC sp_droptype 'birthday'
GO</pre>

As you can see you get the following error
  
Server: Msg 15180, Level 16, State 1, Procedure sp_droptype, Line 32
  
Cannot drop. The data type is being used.

So we first need to drop the table that is using this data type

<pre>drop table TestType</pre>

Now we can try again

<pre>USE master
EXEC sp_droptype 'StateCode'



USE master
EXEC sp_droptype 'birthday'</pre>

And that drops the User-Defined Data Types we created



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][2] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewforum.php?f=22