---
title: Interesting Increase Key Value Post By CSS SQL Server Engineers
author: SQLDenis
type: post
date: 2009-10-26T17:23:50+00:00
ID: 597
url: /index.php/datamgmt/datadesign/interesting-increase-key-value-post-by-c/
views:
  - 8770
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
tags:
  - how to
  - sql server 2000
  - sql server 2005
  - sql server 2008
  - tip

---
The CSS SQL Server Engineers have posted an interesting post, I myself don&#8217;t have any of the fake identity columns but I did many times suggest in newsgroups to use UPDLOCK and HOLDLOCK in a transaction to guarantee that 2 inserts would not generate the same key value. This stuff below is from the CSS SQL Server Engineers post here: http://blogs.msdn.com/psssql/archive/2009/10/26/reduce-locking-and-other-needs-when-updating-data-better-performance.aspx

The following pattern typically stems from an old practice used in SQL 4.x and 6.x days, before IDENTITY was introduced.

sql
begin tran 
declare @iVal int

select @iVal = iVal from CounterTable (HOLDLOCK) where CounterName = 'CaseNumber'

update CounterTable 
set iVal = @iVal + 1 
where CounterName = 'CaseNumber'

commit tran

return @iVal
```

This can be a dangerous construct. Assume that the query is cancelled (attention) right after the select. SQL Server treats this as a batch termination and does not execute other statements. The application now holds a lock under the open transaction and without proper handling it leads to blocking.

One Statement Fix

sql
declare @iVal int

update CounterTable 
set @iVal = iVal = iVal + 1 
where CounterName = 'CaseNumber'

return @iVal
```

So what do you think? Will you use that in the future? Is this too dangerous/unfamiliar?

Take a look at this piece of code that was shown to me by [Emtucifor][1] in this forum post: http://forum.ltd.local/viewtopic.php?f=17&t=7601&start=50#p41079

<div style="border:1px solid black;padding:0 5px 5px 5px;background-color:#ffffdd;">
<h4>
<em>Important Note from <a href="/index.php/All/?disp=authdir&author=71">Emtucifor</a>:</em>
</h4>

<p>
<span class="MT_red" style="display:block;">The information in this section is outdated. It was true in a previous service pack of SQL 2000 but now performs as one would expect.</p> 

<p>
  However, the point about this method being unreliable may still be true: please see the comments on this blog post for a new example of why the syntax:</span><br /> <span style="font-family:Courier New,Courier,monospace;background-color:white;display:block;margin:0 3em;">@var = column = /expression/</span><br /> <span class="MT_red" style="display:block;">does not always work as one might expect.</span>
</p>

<div style="border:1px solid black;padding:5px;">
  <pre lang="tsql">UPDATE #Temp SET  @T = Test1 = 99, @T = Test2 = 47</pre>
  
  <p>
    This proves that behind the scenes SQL Server expands the statement to the following:
  </p>
  
  <pre lang="tsql">UPDATE #Temp SET  @T = 99, @T = 47, Test1 = @T, Test2 = @T</pre>
  
  <p>
    Because of this proof that in fact the @Variable = Column = Expression syntax is not truly coupled in the way it appears to be (otherwise the first update would set Test1 to 99 and Test2 to 47 instead of both to 47), it is best to avoid that syntax entirely.
  </p>
</div></div> 

<p>
  So what do you think? Good or might be prone to some kind of bug in the future and then what?
</p>

<p>
</p>

<p>
  *** <strong>If you have a SQL related question try our <a href="http://forum.ltd.local/viewforum.php?f=17">Microsoft SQL Server Programming</a> forum or our <a href="http://forum.ltd.local/viewforum.php?f=22">Microsoft SQL Server Admin</a> forum</strong><ins></ins>
</p>

 [1]: /index.php/All/?disp=authdir&author=71