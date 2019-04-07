---
title: Avoid Conversions In Execution Plans By Using sp_executesql Instead of Exec
author: SQLDenis
type: post
date: 2009-06-10T13:44:00+00:00
ID: 465
excerpt: |
  Yesterday in the Changing exec to sp_executesql doesn't provide any benefit if you are not using parameters correctly post I showed you that sp_executesql is better than exec because you get plan reuse and the procedure cache doesn't get bloated. 
  Toda&hellip;
url: /index.php/datamgmt/datadesign/avoid-conversions-in-execution-plans-by/
views:
  - 60065
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - dynamic sql
  - exec
  - sp_executesql
  - sql

---
Yesterday in the [Changing exec to sp_executesql doesn&#8217;t provide any benefit if you are not using parameters correctly][1] post I showed you that sp_executesql is better than exec because you get plan reuse and the procedure cache doesn&#8217;t get bloated.
  
Today I will show you that sp_executesql is better than exec or ad hoc queries when you deal with conversions in execution plans

First create this table and populate it with some data

```sql
  create table TestPerf(	id int not null,
        SomeCol1 nvarchar(20),
        SomeValue smallint)

  insert TestPerf values(1,'Test',1)
  insert TestPerf values(2,'Aest',257)
  insert TestPerf values(3,'Best',258)
  insert TestPerf values(4,'Cest',259)
  insert TestPerf values(5,'Dest',251)
  insert TestPerf values(6,'Eest',252)
  insert TestPerf values(7,'Fest',253)
  insert TestPerf values(8,'Gest',254)
  insert TestPerf values(9,'Hest',255)
```

Take a look this query

```sql
select * from TestPerf
where SomeCol1 = 'Test'
and SomeValue = 1
```

Run the query

Here is the execution plan

```
_
   
|&#8211;Table Scan(OBJECT:([testpage].[dbo].[TestPerf]),
   
WHERE:([testpage].[dbo].[TestPerf].[SomeValue]=CONVERT_IMPLICIT(smallint,[@2],0)
   
AND [testpage].[dbo].[TestPerf].[SomeCol1]=CONVERT_IMPLICIT(nvarchar(4000),[@1],0)))_
```

As you can see the value 1 needs to be converted to smallint and &#8216;Test&#8217; Needs to be converted to nvarchar. You can actually see what the value is converted to by using the SQL\_VARIANT\_PROPERTY function.
  
When you run the following query

```sql
  SELECT SQL_VARIANT_PROPERTY(1, 'BaseType') AS [Base Type],
        SQL_VARIANT_PROPERTY(1, 'Precision') AS [PRECISION],
        SQL_VARIANT_PROPERTY(1, 'Scale') AS [Scale]

  union all
  SELECT SQL_VARIANT_PROPERTY('Test', 'BaseType') AS [Base Type],
        SQL_VARIANT_PROPERTY('Test', 'Precision') AS [PRECISION],
        SQL_VARIANT_PROPERTY('Test', 'Scale') AS [Scale]
```

you get this as output

```
  Base Type	PRECISION	Scale
  int	        10	        0
  varchar	        0	        0
```

so the value 1 becomes an int and &#8216;test&#8217; is varchar

Exec with dynamic SQL is not any better than ad hoc of course since the same query gets generated as with the ad hoc query
  
Here is such a query

```sql
declare @SomeValue smallint
declare @SomeCol1 nvarchar(20)

select @SomeValue = 1,@SomeCol1 = 'Test'

declare @SQL nvarchar(1000)
select @SQL = 'select * from dbo.TestPerf
where SomeCol1 = ''' + @SomeCol1 +'''
and SomeValue = ' + convert(nvarchar(10),@SomeValue) 

exec (@SQL)
```

Here is the execution plan

```
|--Table Scan(OBJECT:([testpage].[dbo].[TestPerf]), 
 WHERE:([testpage].[dbo].[TestPerf].[SomeValue]=CONVERT_IMPLICIT(smallint,[@2],0) 
 AND [testpage].[dbo].[TestPerf].[SomeCol1]=CONVERT_IMPLICIT(nvarchar(4000),[@1],0)))
 
```


Ideally you want a query with parameters; below is the quey

```sql
declare @SomeValue smallint
declare @SomeCol1 nvarchar(20)

select @SomeValue = 1,@SomeCol1 = 'Test'

select * from dbo.TestPerf
where SomeCol1 =  @SomeCol1 
and SomeValue = @SomeValue 
```

Here is the execution plan

<pre>|--Table Scan(OBJECT:([testpage].[dbo].[TestPerf]),
 WHERE:([testpage].[dbo].[TestPerf].[SomeValue]=[@SomeValue] 
 AND [testpage].[dbo].[TestPerf].[SomeCol1]=[@SomeCol1]))</pre>

As you can see no conversions here

Now let&#8217;s take a look at how we can take the dynamic query from before and use sp_executesql to get rid of conversions

Run this query

```sql
declare @SomeValue smallint
declare @SomeCol1 nvarchar(20)

select @SomeValue = 1,@SomeCol1 = 'Test'


declare @SQL nvarchar(1000)
select @SQL = 'select * from dbo.TestPerf
where SomeCol1 = @InnerSomeCol1 
and SomeValue =  @InnerSomeValue' 

declare @ParmDefinition nvarchar(500)
SET @ParmDefinition = N'@InnerSomeValue smallint ,@InnerSomeCol1 nvarchar(20)'



exec sp_executesql 	@SQL,@ParmDefinition,
			@InnerSomeValue = @SomeValue,
			@InnerSomeCol1 	= @SomeCol1
      
```
Here is the execution plan

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/ExecPlan1.PNG?mtime=1357609354"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/ExecPlan1.PNG?mtime=1357609354" width="308" height="181" /></a>
</div>

As you can see there were no conversions.

<div style="border:1px solid black;background-color:#444;color:white;margin:0 20px;padding:0 5px 0 5px;">
<span class="MT_larger"><em><strong>How to avoid conversions</strong></em></span><span class="MT_smaller"></p> 

<p>
  </span>The thing that is important with a query like that is that the parameters inside the dynamic sql match the data types of the columns.</div> 
  
  <p>
    So for this part of the query
  </p>
  
<pre>select @SQL = 'select * from dbo.TestPerf
where <strong>SomeCol1 = @InnerSomeCol1</strong> 
and <strong>SomeValue =  @InnerSomeValue'</strong> </pre>
  
  <p>
    The SomeCol1 column and the @InnerSomeCol1 param/variable have to be of the same datatype,<br /> the SomeValue column and the @InnerSomeValue param/variable also have to be of the same datatype in order to prevent conversions
  </p>
  
  <p>
    Let&#8217;s look at something else<br /> The query from before with parameters
  </p>

```sql
declare @SomeValue smallint
declare @SomeCol1 nvarchar(20)

select @SomeValue = 1,@SomeCol1 = 'Test'

select * from dbo.TestPerf
where SomeCol1 =  @SomeCol1 
and SomeValue = @SomeValue 
```

<p>
  Here is the execution plan
</p>

```
 |--Table Scan(OBJECT:([testpage].[dbo].[TestPerf]), 
 WHERE:([testpage].[dbo].[TestPerf].[SomeValue]=[@SomeValue] 
 AND [testpage].[dbo].[TestPerf].[SomeCol1]=[@SomeCol1]))
 
```

    
<p>
  what happens if you change the params to this
</p>

```sql
declare @SomeValue int
declare @SomeCol1 varchar(20)

select @SomeValue = 1,@SomeCol1 = 'Test'

select * from dbo.TestPerf
where SomeCol1 =  @SomeCol1 
and SomeValue = @SomeValue 
```
    
<p>
  Here is the execution plan
</p>
    
```
 |--Table Scan(OBJECT:([testpage].[dbo].[TestPerf]), 
 WHERE:([testpage].[dbo].[TestPerf].[SomeValue]=[@SomeValue] 
 AND [testpage].[dbo].[TestPerf].[SomeCol1]=CONVERT_IMPLICIT(nvarchar(20),[@SomeCol1],0)))
```

<p>
  We still get a conversion on the nvarchar SomeCol1 column
</p>

<p>
  What happens if we change the outer parameter data types in the following dynamic query?
</p>

```sql
declare @SomeValue smallint
declare @SomeCol1 nvarchar(20)

select @SomeValue = 1,@SomeCol1 = 'Test'


declare @SQL nvarchar(1000)
select @SQL = 'select * from dbo.TestPerf
where SomeCol1 = @InnerSomeCol1 
and SomeValue =  @InnerSomeValue' 

declare @ParmDefinition nvarchar(500)
SET @ParmDefinition = N'@InnerSomeValue smallint ,@InnerSomeCol1 nvarchar(20)'

exec sp_executesql 	@SQL,@ParmDefinition,
			@InnerSomeValue = @SomeValue,
			@InnerSomeCol1 	= @SomeCol1
```

<p>
  Here is the execution plan
</p>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/ExecPlan2.PNG?mtime=1357609363"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/ExecPlan2.PNG?mtime=1357609363" width="306" height="177" /></a>
</div>

<p>
  Now we will change the parameters from <strong>smallint </strong>and <strong>nvarchar </strong>to <strong>int </strong>and <strong>varchar </strong>in the query below. This won&#8217;t make any difference in the execution plan because the parameters used in the plan are still of the correct datatype since the inner parameters are used not the outer ones!!
</p>
    
```sql
/*changed from
declare @SomeValue smallint
declare @SomeCol1 nvarchar(20)*/

declare @SomeValue int
declare @SomeCol1 varchar(20)

select @SomeValue = 1,@SomeCol1 = 'Test'


declare @SQL nvarchar(1000)
select @SQL = 'select * from dbo.TestPerf
where SomeCol1 = @InnerSomeCol1 
and SomeValue =  @InnerSomeValue' 

declare @ParmDefinition nvarchar(500)
SET @ParmDefinition = N'@InnerSomeValue smallint ,@InnerSomeCol1 nvarchar(20)'



exec sp_executesql 	@SQL,@ParmDefinition,
			@InnerSomeValue = @SomeValue,
			@InnerSomeCol1 	= @SomeCol1
```

<p>
  Here is the execution plan
</p>

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/ExecPlan3.PNG?mtime=1357609373"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/mongo/ExecPlan3.PNG?mtime=1357609373" width="299" height="182" /></a>
</div>

<p>
  That wraps up this blog post
</p>

<p>
</p>

<p>
  *** <strong>If you have a SQL related question try our <a href="http://forum.ltd.local/viewforum.php?f=17">Microsoft SQL Server Programming</a> forum or our <a href="http://forum.ltd.local/viewforum.php?f=22">Microsoft SQL Server Admin</a> forum</strong><ins></ins>
</p>

 [1]: /index.php/DataMgmt/DataDesign/changing-exec-to-sp_executesql-doesn-t-p