---
title: When Books On Line is not really correct
author: SQLDenis
type: post
date: 2009-07-24T12:45:11+00:00
url: /index.php/datamgmt/dbprogramming/when-books-on-line-is-not-really-correct/
views:
  - 5383
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - books on line
  - bug
  - documentation
  - gotcha
  - sql server 2008

---
A question was posted in our [SQL Server programming forum][1] [today][2]. A person had this stored procedure

<pre>CREATE PROCEDURE TestStuff
@id INT,
@Val1 VARCHAR(20),
@Val2 VARCHAR(20)
AS
 
SELECT @id,@Val1,@Val2</pre>

Executing it like this works

<pre>EXEC TestStuff 1,'test1','Test2'</pre>

However executing it like this also works

<pre>EXEC TestStuff 1,test1,Test2</pre>

So even though you don&#8217;t enclose the character value in quotes it works.

From the [SQL Server 2008 Books Online (June 2009) EXECUTE (Transact-SQL)][3] page, I changed the color to red for the sentence that is wrong.

> value
  
> Is the value of the parameter to pass to the module or pass-through command. If parameter names are not specified, parameter values must be supplied in the order defined in the module.
> 
> When executing pass-through commands against linked servers, the order of the parameter values depends on the OLE DB provider of the linked server. Most OLE DB providers bind values to parameters from left to right.
> 
> If the value of a parameter is an object name, **<span class="MT_red">character string, or qualified by a database name or schema name, the whole name must be enclosed in single quotation marks</span>**. If the value of a parameter is a keyword, the keyword must be enclosed in double quotation marks.
> 
> If a default is defined in the module, a user can execute the module without specifying a parameter.
> 
> The default can also be NULL. Generally, the module definition specifies the action that should be taken if a parameter value is NULL.

As you can see that is not right at all, I checked Books on line for SQL Server 2000 and it basically has the same information. I am surprised that nobody has alerted Microsoft about this.

So now that we know that you can do that, then why can you not do this?

<pre>DECLARE @v VARCHAR(20)
 
SELECT @v = a
 
SELECT @v</pre>

That gives you this error
  
Server: Msg 207, Level 16, State 3, Line 3
  
Invalid column name &#8216;a&#8217;.

Of course there are other inconsistent things in SQL Server, here is a perfect example

Varchar defaults to 1 character here

<pre>DECLARE @v VARCHAR
SELECT @v = 'aaaaaa'
SELECT @v</pre>

Varchar defaults to 30 characters here

<pre>SELECT CONVERT(VARCHAR,'aaaaa')</pre>

George Mastros wrote a nice blog post about this here: [SQL Server Stored Procedure with nvarchar parameter][4]

So what do you think, should I file an item for this on connect?



\*** **If you have a SQL related question try our [Microsoft SQL Server Programming][1] forum or our [Microsoft SQL Server Admin][5] forum**<ins></ins>

 [1]: http://forum.lessthandot.com/viewforum.php?f=17
 [2]: http://forum.lessthandot.com/viewtopic.php?f=17&t=6856
 [3]: http://msdn.microsoft.com/en-us/library/ms188332.aspx
 [4]: /index.php/DataMgmt/DataDesign/sql-server-stored-procedure-with-nvarcha
 [5]: http://forum.lessthandot.com/viewforum.php?f=22