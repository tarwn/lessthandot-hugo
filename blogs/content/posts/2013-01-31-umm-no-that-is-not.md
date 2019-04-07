---
title: Umm….No…that is not how you export a table into a file with SQL Server
author: SQLDenis
type: post
date: 2013-01-31T22:28:00+00:00
ID: 1954
excerpt: 'Sometimes the creativity of the human species is truly amazing. Someone needed to export a table into a file from within SQL Server. Now there are several ways to do this like bcp, export wizard, Query..Results To File, SSIS etc etc. Today I noticed som&hellip;'
url: /index.php/datamgmt/dbprogramming/umm-no-that-is-not/
views:
  - 25938
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
tags:
  - export
  - files
  - sql server 2008
  - sql server 2008 r2
  - sql server 2012

---
Sometimes the creativity of the human species is truly amazing. Someone needed to export a table into a file from within SQL Server. Now there are several ways to do this like bcp, export wizard, Query..Results To File, SSIS etc etc. Today I noticed someone found yet another way&#8230;.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/UmmNo.PNG?mtime=1359677914"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/ADvent/UmmNo.PNG?mtime=1359677914" width="304" height="409" /></a>
</div>

This person decided to use a SQL Agent job to create the file. That is still not strange but here comes the interesting part&#8230;

Here is what the person did, first a job was added with a query.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/FubarJob.PNG?mtime=1359678193"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/FubarJob.PNG?mtime=1359678193" width="684" height="614" /></a>
</div>

Then in the Advanced part of the step an output file was chosen

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/FubarJobAdvanced.PNG?mtime=1359678202"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/FubarJobAdvanced.PNG?mtime=1359678202" width="512" height="443" /></a>
</div>

This of course will create the file, however there will be some output in the from the job itself

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/FubarJobOutput.PNG?mtime=1359678223"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Denis/Oracle/FubarJobOutput.PNG?mtime=1359678223" width="666" height="292" /></a>
</div>

The only reason I found out about this was because I was asked how to skip the &#8216;header&#8217; in SQL Server. After a good deal of puzzlement looking at this creative &#8216;solution&#8217; I told the person how to use bcp instead

Any crazy stories from the trenches you want to share in the comments?