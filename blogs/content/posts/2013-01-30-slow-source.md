---
title: Slow source? Make your data flow buffers smaller!
author: Koen Verbeeck
type: post
date: 2013-01-30T12:15:00+00:00
excerpt: This blog post describes how making the data flow buffers smaller might give a performance boost in some scenarios.
url: /index.php/datamgmt/dbprogramming/mssqlserver/slow-source/
views:
  - 19579
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - SSIS
tags:
  - buffer
  - data flow
  - defaultbuffermaxrows
  - defaultbuffersize
  - performance tuning
  - sql server
  - ssis

---
<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SlowSource/PropertySettings.PNG?mtime=1359554797"><img style="float: left;" src="/wp-content/uploads/users/koenverbeeck/SlowSource/PropertySettings.PNG?mtime=1359554797" alt="" width="222" height="84" /></a>
</p>

<p style="text-align: justify;">
  Whoa whoah. Aren’t you supposed to increase your data flow buffer size in order to speed up your packages? If you have enough memory and you can process more rows at the same time because your buffer is larger, that’s what we want, right? Yes, this is confirmed by the old blog post <a href="http://blogs.msdn.com/b/sqlperf/archive/2007/05/11/adjust-buffer-size-in-ssis-data-flow-task.aspx">Adjust buffer size in SSIS data flow task</a> by the SQL Server Performance Team. But this is only true when your source is fast enough to fill those buffers. If you have very large buffers, the remainder of the data flow is just waiting for the slow source to fill a buffer, which is just time going to waste.
</p>

<span style="text-align: justify;">Rob Farley (</span><a style="text-align: justify;" href="http://sqlblog.com/blogs/rob_farley/default.aspx">blog </a><span style="text-align: justify;">| </span><a style="text-align: justify;" href="https://twitter.com/rob_farley">twitter</a><span style="text-align: justify;">) describes the concept in his excellent blog post </span><a style="text-align: justify;" href="http://sqlblog.com/blogs/rob_farley/archive/2011/02/17/the-ssis-tuning-tip-that-everyone-misses.aspx">The SSIS tuning tip that everyone misses</a><span style="text-align: justify;">. Basically, it’s about filling your buffers with data as soon as possible, so other data flow tasks can start working on it. Rob achieved his goal by specifying a query hint, but you can do the same by making your buffers smaller. Because, a smaller buffer takes less time to be filled with data and can be passed on the data flow much quicker. Jamie Thomson (</span><a style="text-align: justify;" href="http://sqlblog.com/blogs/jamie_thomson/default.aspx">blog</a> <span style="text-align: justify;">| </span><a style="text-align: justify;" href="https://twitter.com/jamiet">twitter</a><span style="text-align: justify;">) describes the effect in his blog post </span><a style="text-align: justify;" href="http://consultingblogs.emc.com/jamiethomson/archive/2007/12/18/SSIS_3A00_-A-performance-tuning-success-story.aspx">SSIS: A performance tuning success story</a><span style="text-align: justify;">. I also encountered a similar story in a </span><a style="text-align: justify;" href="http://www.sqlservercentral.com/Forums/Topic1404429-364-1.aspx#bm1404567">forum thread</a><span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SlowSource/Pending.PNG?mtime=1359554789"><img style="float: left;" src="/wp-content/uploads/users/koenverbeeck/SlowSource/Pending.PNG?mtime=1359554789" alt="" width="157" height="154" /></a>
</p>

<p style="text-align: justify;">
  I’ll share on of my success stories as well. In my recent Oracle migration, I was transferring a table from Oracle to SQL Server using SSIS. The table wasn’t really large, only about 90,000 rows, but one column contained XML files. These were stored in the Oracle database as a CLOB column (Character Large Object) and in SQL Server as a NVARCHAR(MAX) column. Some of these XML could be quite large, some up to 50MB. When I ran my package using the default settings, 10MB for <em>DefaultBufferSize</em> and 10,000 for <em>DefaultBufferMaxRows</em>, I had to store a long time at a yellow source, without any data being transferred.
</p>

<span style="text-align: justify;">After almost 35 minutes, the package finished loading all the rows into SQL Server.</span>

<p style="text-align: justify;">
  <a style="text-align: center;" href="/media/users/koenverbeeck/SlowSource/Output1.PNG?mtime=1359554758"><img src="/wp-content/uploads/users/koenverbeeck/SlowSource/Output1.PNG?mtime=1359554758" alt="" width="796" height="190" /></a>
</p>

<span style="text-align: justify;">However, when I changed the </span>_DefaultBufferMaxRows_ <span style="text-align: justify;">to 500, the package finished in a mere 11 minutes!</span>

<p style="text-align: justify;">
  <a style="text-align: center;" href="/media/users/koenverbeeck/SlowSource/Output2.PNG?mtime=1359554763"><img src="/wp-content/uploads/users/koenverbeeck/SlowSource/Output2.PNG?mtime=1359554763" alt="" width="792" height="184" /></a>
</p>

<span style="text-align: justify;">To make sure this incredible speed-up wasn’t the result of any caching on the source, I ran the package again with the default settings:</span>

<p style="text-align: justify;">
  <a style="text-align: center;" href="/media/users/koenverbeeck/SlowSource/Output3.PNG?mtime=1359554783"><img src="/wp-content/uploads/users/koenverbeeck/SlowSource/Output3.PNG?mtime=1359554783" alt="" width="794" height="181" /></a>
</p>

<span style="text-align: justify;">Possible caching seems to nibble 2 minutes off (or it just might be coincidence), but it isn’t responsible for making the packages run three times as fast.</span>

<p style="text-align: justify;">
  Why the big difference? I created an Excel graph displaying the size of the CLOB column for the first 20,000 rows, which roughly equals 2 buffers when the default settings are used. I used the function <a href="http://docs.oracle.com/cd/B19306_01/appdev.102/b14258/d_lob.htm">DBMS_LOB.getlength</a> to get the number of characters in a particular XML file in the CLOB column. Assuming every character equals one byte, this is the same as the size in bytes. I’m educated as an engineer, so g = 10, &#928; = 3 and my CLOB columns contains only singe byte characters and no multi-byte characters 🙂 If there are multi-byte characters present, the following values in the graph represent the possible minimal size of the XML value. It might be bigger in reality.
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/SlowSource/Graph.PNG?mtime=1359554752"><img src="/wp-content/uploads/users/koenverbeeck/SlowSource/Graph.PNG?mtime=1359554752" alt="" width="507" height="317" /></a>
</div>

<span style="text-align: justify;">We can see that around row 2800 a 20 megabyte CLOB value shows up, followed by several other large XML files. In the second buffer we have even larger XML files, one of 25MB and one of 40MB. Needless to say, it takes a while for SSIS can fill a buffer with this large data. Once a buffer is full, it is passed immediately to the destination. It’s possible the next buffer is very quickly populated if the next rows contain only XML files of a few kilobytes large. But because the destination is still processing the previous large buffer, we get an effect called </span>_pipeline backpressure_<span style="text-align: justify;">, which is described in detail by Todd McDermid (</span><a style="text-align: justify;" href="http://toddmcdermid.blogspot.be/">blog </a><span style="text-align: justify;">| </span><a style="text-align: justify;" href="https://twitter.com/Todd_McDermid">twitter</a><span style="text-align: justify;">) in his blog post </span><a style="text-align: justify;" href="http://toddmcdermid.blogspot.be/2011/07/what-is-pipeline-backpressure.html">What is Pipeline Backpressure?</a><span style="text-align: justify;">. When we combine these two effects – a slow source and an occasional pipeline backpressure – we get a very slow package.</span>

<p style="text-align: justify;">
  When we use a much smaller buffer – 500 rows in my case – the source can already fill up a few buffers before the first large XML is reached. This keeps the destination busy while the source processes these big rows. Because the larger XML files are uniformly distributed over the table, we can take full advantage of this effect, cutting the total package runtime to one third.
</p>

<p style="text-align: justify;">
  <strong>Conclusion</strong>
</p>

<p style="text-align: justify;">
  When dealing with a slow source, it might be beneficial to lower the size of the data flow buffer in order to get better performance. Don’t do this all the time! Most of the time the default settings are good enough and if the source is fast you might benefit from bigger buffers. As always, test test test and then put it in production.
</p>