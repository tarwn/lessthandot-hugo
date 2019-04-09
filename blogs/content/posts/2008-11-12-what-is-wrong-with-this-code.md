---
title: What is wrong with this code?
author: SQLDenis
type: post
date: 2008-11-12T13:51:24+00:00
ID: 202
url: /index.php/datamgmt/datadesign/what-is-wrong-with-this-code/
views:
  - 5501
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Programming
  - Microsoft SQL Server
tags:
  - coding
  - sql server 2005
  - sql server 2008

---
Take a look at this code which I found a while back in a stored proc

sql
declare @id int,@xtype char(1),@uid int,@info int,@status int

set  @id =(select id from sysobjects where name = 'sysobjects')
set @xtype  =(select xtype from sysobjects where name = 'sysobjects')
set @uid   =(select uid from sysobjects where name = 'sysobjects')
set @info  =(select info from sysobjects where name = 'sysobjects')
set @status =(select status from sysobjects where name = 'sysobjects')

select @id ,@xtype ,@uid ,@info ,@status 
go
```

Do you see what is wrong? It uses five select statements to accomplish something which can be done in one. I would do something like this instead.

sql
declare @id int,@xtype char(1),@uid int,@info int,@status int

select @id =id
,@xtype =xtype
,@uid =uid
,@info =info
,@status =status 
from sysobjects where name = 'sysobjects'

select @id ,@xtype ,@uid ,@info ,@status
```

Let's take a look at another example.

What we want to do is display a row of counts for 4 xtypes from the sysobjects table, here is an example

<pre>s	u	p	c
19	143	74	6</pre>

Have you ever seen code like this that does that? I have!

sql
select count(*) as [s],
(select count(*) from  sysobjects where xtype = 'u') as [u],
(select count(*) from  sysobjects where xtype = 'p') as [p],
(select count(*) from  sysobjects where xtype = 'c') as [c] 
from  sysobjects 
where xtype = 's'
```

That code will do a select 4 times against the table
  
A better way would be to do this

sql
select  sum(case xtype when 's' then 1 else 0 end) as [s],
sum(case xtype when 'u' then 1 else 0 end) as [u],
sum(case xtype when 'p' then 1 else 0 end) as [p],
sum(case xtype when 'c' then 1 else 0 end) as [c] 
from sysobjects 
where xtype in('s','u','p','c')
```

In SQL server 2005/2008 you can use the PIVOT operator, here is what the query would look like

sql
SELECT s, u, p, c
FROM
(SELECT xtype
FROM sysobjects
WHERE xtype IN('s','u','p','c')) AS pivTemp
PIVOT
(   count(xtype) 
    FOR xtype IN(s, u, p, c)
) AS pivTable
```

If you can think of any other examples feel free to leave a comment