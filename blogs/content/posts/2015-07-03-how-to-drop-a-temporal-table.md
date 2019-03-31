---
title: How to drop a Temporal Table
author: Koen Verbeeck
type: post
date: 2015-07-03T07:39:59+00:00
url: /index.php/uncategorized/how-to-drop-a-temporal-table/
views:
  - 8181
rp4wp_auto_linked:
  - 1
categories:
  - Uncategorized
tags:
  - business intelligence
  - sql 2016
  - sql server
  - syndicated
  - temporal table

---
<p style="text-align: justify">
  No no, I didn&#8217;t say <em>temporary</em>, but <strong>temporal</strong>! SQL Server 2016 introduces a great new feature called <strong>Temporal Tables</strong>. Or in other words, system-versioned tables. We&#8217;ll see what catches on. In a gist, such a table keeps track of the history of its rows by the use of some audit columns (start and end date) and a history table. Sounds a bit like the love child of Change Data Capture and a Type 2 dimension.
</p>

<p style="text-align: justify">
  Anyway, I was testing this feature a bit (more specifically if they supported computed columns as promised in CTP 2.1) and I created a temporal table with the following query:
</p>

<pre>CREATE TABLE dbo.TestTemporal
		(ID int primary key
		,A int
		,B int
		,C AS A * B
		,SysStartTime datetime2 GENERATED ALWAYS AS ROW START NOT NULL
		,SysEndTime datetime2 GENERATED ALWAYS AS ROW END NOT NULL
		,PERIOD FOR SYSTEM_TIME (SysStartTime,SysEndTime)) WITH(SYSTEM_VERSIONING = ON);</pre>

<p style="text-align: justify">
  This code only works in CTP2.1, not in earlier versions of SQL 2016 because of the computed column specification.<br /> Anyway, of course I forgot to set my database so this table was created in the master database. I wanted to drop it, but I was greeted with this lovely message:
</p>

<p style="text-align: justify">
  <a href="http://blogs.ltd.local/wp-content/uploads/2015/07/errormessage.png"><img class="alignnone size-full wp-image-3466" src="http://blogs.ltd.local/wp-content/uploads/2015/07/errormessage.png" alt="errormessage" width="716" height="151" srcset="http://blogs.ltd.local/wp-content/uploads/2015/07/errormessage.png 716w, http://blogs.ltd.local/wp-content/uploads/2015/07/errormessage-300x63.png 300w" sizes="(max-width: 716px) 100vw, 716px" /></a>
</p>

<p style="text-align: justify">
  Even the <em>Delete</em> action was missing from the table&#8217;s context menu.
</p>

<p style="text-align: justify">
  <a href="http://blogs.ltd.local/wp-content/uploads/2015/07/nodelete.png"><img class="alignnone size-full wp-image-3467" src="http://blogs.ltd.local/wp-content/uploads/2015/07/nodelete.png" alt="nodelete" width="312" height="364" srcset="http://blogs.ltd.local/wp-content/uploads/2015/07/nodelete.png 312w, http://blogs.ltd.local/wp-content/uploads/2015/07/nodelete-257x300.png 257w" sizes="(max-width: 312px) 100vw, 312px" /></a>
</p>

<p style="text-align: justify">
  A quick look on the MSDN page <a href="https://msdn.microsoft.com/en-us/library/dn935015.aspx">Temporal Tables</a> teaches us that dropping a system-versioned table is a <em>disallowed Alter Schema operation</em>. No luck there. The answer came, as usual, through Twitter:
</p>

<blockquote class="twitter-tweet" lang="en">
  <p dir="ltr" lang="en">
    <a href="https://twitter.com/Ko_Ver">@Ko_Ver</a> did you try to remove temporal setting before dropping it?
  </p>
  
  <p>
    — Janos Berke (@JanosBerke) <a href="https://twitter.com/JanosBerke/status/615491933146861568">June 29, 2015</a>
  </p>
</blockquote>

<p style="text-align: justify">
  Turning the system-versioning off and dropping the table is the answer to our question (and to about any schema operation you want to do on a system-versioned table). If we execute such an ALTER TABLE script, we get the following:
</p>

<p style="text-align: justify">
  <a href="http://blogs.ltd.local/wp-content/uploads/2015/07/after_alter.png"><img class="alignnone size-full wp-image-3468" src="http://blogs.ltd.local/wp-content/uploads/2015/07/after_alter.png" alt="after_alter" width="830" height="99" srcset="http://blogs.ltd.local/wp-content/uploads/2015/07/after_alter.png 830w, http://blogs.ltd.local/wp-content/uploads/2015/07/after_alter-300x35.png 300w" sizes="(max-width: 830px) 100vw, 830px" /></a>
</p>

<p style="text-align: justify">
  The temporal table is reduced to a normal table, which means it can be dropped as a normal table. But the history table is kept as well. This means you can always re-enable system-versioning if you want to.<br /> The name of the history table is dbo.MSSQL_TemporalHistoryFor_xxx, where xxx is the object id of the main table. Something to keep in mind if you want to automate scripts. Or just specify a friendly name when enabling system-versioning.
</p>