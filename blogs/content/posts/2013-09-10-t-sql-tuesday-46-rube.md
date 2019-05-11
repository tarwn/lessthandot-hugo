---
title: 'T-SQL Tuesday #46: Rube Goldberg Machine'
author: Koen Verbeeck
type: post
date: 2013-09-10T13:46:00+00:00
ID: 2153
excerpt: |
   
  
   
  It's the second Tuesday of the month, and you know what time it is! That's right, another installment of T-SQL Tuesday which is hosted this month by Rick Krueger (blog | twitter). The topic is about that one time we did a hack to get something s&hellip;
url: /index.php/datamgmt/ssrs/t-sql-tuesday-46-rube/
views:
  - 24247
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS
tags:
  - dashboard
  - indicator
  - kpi
  - performance
  - report
  - ssrs
  - syndicated

---
[<img style="float: left;" src="/wp-content/uploads/users/koenverbeeck/TSQL2sday37/TSQL2sday.PNG?mtime=1355209029" alt="" width="133" height="134" />][1]

<span style="text-align: justify;">It's the second Tuesday of the month, and you know what time it is! That's right, another installment of T-SQL Tuesday which is hosted this month by Rick Krueger (</span><a style="text-align: justify;" href="http://www.dataogre.com/">blog</a> <span style="text-align: justify;">| </span><a style="text-align: justify;" href="https://twitter.com/DataOgre">twitter</a><span style="text-align: justify;">). The topic is about that one time we did a hack to get something sorted out, because of time pressure, budget, sheer laziness or whatever the reason was.</span>

My story is not about a hack in its purest definition, but it is about a nice alternative way to get something done more efficiently. I'm talking about [Indicators][2] in a SSRS report. For those non-BI folks who just fell off their chair: an indicator is a visual component introduced in Reporting Services 2008R2. They are very tiny gauges that display the state of a single data value at a glance. They come in various shapes and colors and they are pretty useful for KPIs and dashboards.

[<img src="/wp-content/uploads/users/koenverbeeck/TSQL2sday46/indicators.PNG?mtime=1378820544" alt="" width="403" height="362" />][3]

<span style="text-align: justify;">Once upon a time I had a fairly large matrix report at a client. There were a lot of rows and a lot of columns in that matrix, and the client wanted an indicator in every cell of the matrix. The development went pretty OK using BIDS, although it's quite a pain to configure a separate indicator for every column. However, when the report was deployed to the server, it became nerve wreckingly slow. This was caused by the sheer amount of indicators, as the query behind the report finished in less than 1 second. Time to look for an alternative.</span>

<p style="text-align: justify;">
  After some Google fu, I came across this MSDN topic: <a href="http://social.msdn.microsoft.com/Forums/sqlserver/en-US/e513aead-5787-4d97-8309-1eaf02a1c3d7/ssrs-2008-r2-rendering-performance">SSRS 2008 R2 Rendering Performance</a>. In this thread, the alternative of using Wingdings was suggested, instead of using actual indicators. You know Wingdings, the Windows font with all the funny symbols.
</p>

<p style="text-align: justify;">
  I replaced every indicator of an expression of the following type:
</p>

<p style="text-align: justify;">
  <em>=iif((Fields!Result) < 70,"O","P"))</em>
</p>

<p style="text-align: justify;">
  The letter O in Wingdings-2 corresponds with a cross, the letter P corresponds with a checkmark. I placed a similar expression on the font color, making the cross red and the checkmark green. The resulting matrix looked like this:
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/TSQL2sday46/results.PNG?mtime=1378820544"><img src="/wp-content/uploads/users/koenverbeeck/TSQL2sday46/results.PNG?mtime=1378820544" alt="" width="563" height="210" /></a>
</p>

<span style="text-align: justify;">Since the report now just had to evaluate a simple expression and display a simple text for each cell instead of a single chart for each cell, the report performance went through the roof.</span>

<p style="text-align: justify;">
  In conclusion: using something "goofy" can sometimes lead to extraordinary results.
</p>

 [1]: http://www.dataogre.com/2013/09/02/t-sql-tuesday-46-rube-goldberg-machine/
 [2]: http://technet.microsoft.com/en-us/library/ee633651.aspx
 [3]: /media/users/koenverbeeck/TSQL2sday46/indicators.PNG?mtime=1378820544