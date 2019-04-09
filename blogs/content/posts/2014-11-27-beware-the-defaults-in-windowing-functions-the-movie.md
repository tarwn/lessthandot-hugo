---
title: Beware the defaults! (in windowing functions) – The Movie!
author: Koen Verbeeck
type: post
date: 2014-11-27T14:30:53+00:00
ID: 3071
url: /index.php/datamgmt/dbprogramming/mssqlserver/beware-the-defaults-in-windowing-functions-the-movie/
views:
  - 4919
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - online training
  - sql server
  - syndicated
  - t-sql
  - webucator
  - windowing functions

---
<p style="text-align: justify;">
  I was recently contacted by the fine gents of <a href="https://www.webucator.com/">Webucator</a>, an online training services provider. In order to promote their <a href="https://www.webucator.com/database/mssql.cfm">SQL Server classes</a>, they are doing a free series called <em>SQL Server Solutions from the Web</em> where they show different SQL Server solutions found in blog posts around the web. Essentially, they are turning blog posts into videos. They asked me if they could turn my blog post <a href="/index.php/datamgmt/dbprogramming/mssqlserver/beware-the-defaults-in-windowing-functions/">Beware the defaults! (in windowing functions)</a> into such a video and &#8211; humble as I am &#8211; I gave them permission to do so. And the result is now here for everyone to watch:
</p>

{{< youtube nBUnoVRjVSA >}}

<p style="text-align: justify;">
  <a href="https://www.youtube.com/watch?v=nBUnoVRjVSA">URL to Youtube video</a>
</p>

<p style="text-align: justify;">
  I'm quite pleased with the video &#8211; excellent <a href="http://technet.microsoft.com/en-us/sysinternals/bb897434.aspx">Zoomit </a>use by the way &#8211; as it highlights the most important aspects of my blog post:
</p>

<ul style="text-align: justify;">
  <li>
    For regular aggregation functions using the OVER clause, do not use the ORDER BY unless necessary because this invokes the horrible default of RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW.
  </li>
  <li>
    If you do use the ORDER BY specify a correct sorting order.
  </li>
</ul>

<p style="text-align: justify;">
  Or even better, specify ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW or another framing option. Just not the default 🙂<br /> The one thing missing in the video &#8211; and I must confess I didn't highlight it enough in my blog post &#8211; is the massive performance difference between ROWS and RANGE. I'm re-reading the excellent book by Itzik Ben-Gan about the <a href="http://www.amazon.com/Microsoft-High-Performance-Functions-Developer-Reference/dp/0735658366/ref=sr_1_1?ie=UTF8&qid=1417091814&sr=8-1&keywords=windowing+functions">T-SQL windowing functions</a> and there Itzik explains why this is the case. In a nutshell:
</p>

<p style="text-align: justify;">
  When using the ROWS window frame extent, the window spool operator can use an optimized in-memory work table which speeds things up tremendously. However, when using RANGE the typical on-disk work table has to be used, which is of course much slower. In theory, RANGE is equal to ROWS when the ordering values are unique within the partition, but the optimizer doesn't check for uniqueness so RANGE will always default to the on-disk work table.
</p>

<p style="text-align: justify;">
  If you were not convinced to always specify ROWS instead of the default RANGE, I hope you now are.
</p>