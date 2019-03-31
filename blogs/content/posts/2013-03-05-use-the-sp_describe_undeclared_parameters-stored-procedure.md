---
title: Use the sp_describe_undeclared_parameters stored procedure to check if dynamic SQL has undeclared parameters
author: SQLDenis
type: post
date: 2013-03-06T00:22:00+00:00
excerpt: |
  Let's say you have the following piece of dynamic SQL
  
  DECLARE @DynamicSql nvarchar(1000)
  SELECT @DynamicSql = N'DECLARE @Type nchar(3) = ''P'' SELECT *
  FROM master..spt_values where type =@Type'
  
  exec( @DynamicSql )
  
  That will execute just fine&hellip;
url: /index.php/datamgmt/dbprogramming/use-the-sp_describe_undeclared_parameters-stored-procedure/
views:
  - 8198
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Sometimes you get some dynamic SQL handed to you which is 2 pages long with all kind of parameters. Of course you yourself would never write such a monstrosity, but how can you verify that all parameters in the code have been declared? Let&#8217;s say you have the following piece of dynamic SQL

<pre>DECLARE @DynamicSql nvarchar(1000)
SELECT @DynamicSql = N'DECLARE @Type nchar(3) = ''P'' SELECT *
FROM master..spt_values where type =@Type'

exec( @DynamicSql )</pre>

That will execute just fine. What if you forgot the `DECLARE @Type nchar(3) = ''P''` part?

<pre>DECLARE @DynamicSql nvarchar(1000)
SELECT @DynamicSql = N'SELECT *
FROM master..spt_values where type =@Type'

exec( @DynamicSql )</pre>

Here is the error.
  
_Msg 137, Level 15, State 2, Line 2
  
Must declare the scalar variable &#8220;@Type&#8221;._

What if there was something that could help you find these kind of problems? There is, in SQL Server 2012 we now have the sp\_describe\_undeclared_parameters stored procedure 

Here is how Books On Line describes the sp\_describe\_undeclared_parameters stored procedure

> Returns a result set that contains metadata about undeclared parameters in a Transact-SQL batch. Considers each parameter that is used in the @tsql batch, but not declared in @params. A result set is returned that contains one row for each such parameter, with the deduced type information for that parameter. The procedure returns an empty result set if the @tsql input batch has no parameters except those declared in @params.

Here is how you can easily test the code. Change `exec( @DynamicSql )` to `exec sp_describe_undeclared_parameters  @DynamicSql`

<pre>DECLARE @DynamicSql nvarchar(1000)
SELECT @DynamicSql = N'SELECT *
FROM master..spt_values where type =@Type'

EXEC sp_describe_undeclared_parameters  @DynamicSql</pre>

Here is what you see 

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/sp_describe_undeclared_parameters.PNG?mtime=1362535594"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/sp_describe_undeclared_parameters.PNG?mtime=1362535594" width="631" height="226" /></a>
</div>

There are more columns in the output, feel free to run this yourself to see all of them. Basically it will give you type, precision, length parameter name and more

How many times have you done something like the following

<pre>EXEC sp_executesql @tsql = 
N'SELECT id, name, xtype 
FROM sys.sysobjects
WHERE id = @id OR NAME = @name',
@params = N'@id int',@id = 1,@name = 'sysobjects'</pre>

Running that will give you the following error

_Msg 137, Level 15, State 2, Line 3
  
Must declare the scalar variable &#8220;@name&#8221;._

Here is how to quickly test that as well, take the code from above and grab everything except for this part ,@id = 1,@name = &#8216;sysobjects&#8217;

Basically, you now have this

<pre>exec sp_executesql @tsql = 
N'SELECT id, name, xtype 
FROM sys.sysobjects
WHERE id = @id OR NAME = @name',
@params = N'@id int'</pre>

Now change sp\_executesql to sp\_describe\_undeclared\_parameters 

Run that

<pre>exec sp_describe_undeclared_parameters @tsql = 
N'SELECT id, name, xtype 
FROM sys.sysobjects
WHERE id = @id OR NAME = @name',
@params = N'@id int'</pre>

Here is what you get

<pre>parameter_ordinal name	suggested_system_type_id suggested_system_type_name	
1	          @name	231	                 nvarchar(128)	</pre>

Again there are more columns so feel free to run this yourself. So far I showed you examples with just one parameter, the stored procedure will return all undeclared parameters

Here is what it looks like with 4 undeclared parameters

<pre>DECLARE @DynamicSql nvarchar(1000)
SELECT @DynamicSql = N'SELECT *
FROM master..spt_values where type =@Type
AND Name =@Name
And number =@number
and high = @high'

EXEC sp_describe_undeclared_parameters  @DynamicSql</pre>

Here is the output

<pre>parameter_ordinal name suggested_system_type_id	suggested_system_type_name suggested_max_length
1	         @Type	239	                nchar(3)	           6
2	         @Name	231	                nvarchar(35)	          70
3	        @number	56	                int	                   4
4	          @high	56	                int	                   4</pre>

As you can see the sp\_describe\_undeclared_parameters stored procedure can come in handy to check if dynamic SQL has undeclared parameters