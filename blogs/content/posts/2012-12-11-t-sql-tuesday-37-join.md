---
title: 'T-SQL Tuesday #37: Join me in a month of Joins'
author: Koen Verbeeck
type: post
date: 2012-12-11T05:25:00+00:00
ID: 1845
excerpt: |
   
  This month's T-SQL Tuesday is hosted by Sebastian Meine (blog | twitter) and the topic is joins. He has a whole month worth of topics about joins: A Join A Day – Introduction.
  
  My blog post is about something weird I encountered while&hellip;
url: /index.php/datamgmt/dbprogramming/mssqlserver/t-sql-tuesday-37-join/
views:
  - 29529
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - Oracle
tags:
  - joins
  - oracle
  - sql server
  - t-sql tuesday

---
<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">This month's T-SQL Tuesday is hosted by Sebastian Meine (<a href="http://sqlity.net/en/">blog</a> | <a href="https://twitter.com/sqlity">twitter</a>) and the topic is <strong>joins</strong>. He has a whole month worth of topics about joins: <a href="http://sqlity.net/en/1146/a-join-a-day-introduction/">A Join A Day – Introduction</a>.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US"> </span>
</p>

<div class="image_block">
  <a href="http://sqlity.net/en/1175/t-sql-tuesday-37-invite-to-join-me-in-a-month-of-joins/"><img style="float: left;" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/TSQL2sday37/TSQL2sday.PNG?mtime=1355209029" alt="" width="133" height="134" /></a>
</div>

 

 

 

 

 

My blog post is about something weird I encountered while I was converting some old Oracle code to T-SQL (I encounter all sorts of weird stuff in Oracle code, but let's stay on topic). Amongst all the usual cursor-madness, I stumbled upon this query:

```SQL
SELECT mt1.Column1, mt2.Column2
FROM [dbo].[MyTable1] mt1, [dbo].[MyTable2] mt2
WHERE      mt1.[JoinColumn1] (+)= mt2.[JoinColumn1]
       AND mt1.[JoinColumn2] (+)= mt2.[JoinColumn2]
```

<span style="text-align: justify;">The original query was much longer and more complex, but I kept only the necessary parts. I recognised the old-style of writing joins, also known as ANSI-89 joins (read more about them in </span><a style="text-align: justify;" href="http://sqlblog.com/blogs/aaron_bertrand/archive/2009/10/08/bad-habits-to-kick-using-old-style-joins.aspx">Bad habits to kick : using old-style JOINs</a><span style="text-align: justify;">). However, I never came across the type of join designated by a (+)=. Most likely this has something to do what my lack of experience with Oracle, which I am very proud of :). SQL Server didn't recognise the syntax so it had to be either Oracle only, or something very outdated. I asked about this on Twitter and a few people came up with </span><a style="text-align: justify;" href="http://msdn.microsoft.com/en-us/library/cc645922.aspx">compound operators</a> <span style="text-align: justify;">which were introduced in SQL Server 2008, but luckily Mladen Prajdic (</span><a style="text-align: justify;" href="http://weblogs.sqlteam.com/mladenp/default.aspx">blog</a> <span style="text-align: justify;">| </span><a style="text-align: justify;" href="https://twitter.com/MladenPrajdic">twitter</a><span style="text-align: justify;">) vaguely remembered those could be LEFT OUTER JOINs.</span>

<p class="MsoNormal">
  <span lang="EN-US"><em>* UPDATE: Apparently this syntax coresponds with RIGHT OUTER JOIN. For more info, see Robs comment below *</em></span>
</p>

<p class="MsoNormal">
  <span lang="EN-US">And he was right. After some Googling I found this Oracle doc page: <a href="http://docs.oracle.com/cd/B19306_01/server.102/b14200/queries006.htm">Joins</a>. It is described as the "Oracle join operator". They advise not to use it, but you know, stuff like this always pops up in legacy code.</span>
</p>

<p class="MsoNormal">
  <span lang="EN-US">So, if you're converting some old Oracle code (we have to get rid of it once, don't we?), now you know how to deal with this monstrosity.</span>
</p>