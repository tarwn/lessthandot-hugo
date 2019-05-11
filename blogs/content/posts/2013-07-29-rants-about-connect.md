---
title: Rants about Connect
author: Koen Verbeeck
type: post
date: 2013-07-29T12:49:00+00:00
ID: 2141
excerpt: 'The site connect.microsoft.com is a wonderful concept: you can provide feedback about the Microsoft products, report bugs and give suggestions directly to the teams responsible for those products. People can vote on items to indicate them as more import&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/rants-about-connect/
views:
  - 35009
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Excel Reporting
  - Microsoft SQL Server
  - SSRS
tags:
  - connect
  - microsoft
  - powerpivot
  - sql server
  - ssas tabular
  - ssrs
  - syndicated

---
<p style="text-align: justify;">
  The site <a href="/connect.microsoft.com">connect.microsoft.com</a> is a wonderful concept: you can provide feedback about the Microsoft products, report bugs and give suggestions directly to the teams responsible for those products. People can vote on items to indicate them as more important.
</p>

<p style="text-align: justify;">
  At least, that's the theory. It's been known that the communication from Microsoft can be improved. For example: bugs that are closed as "by design" of "not reproducible" without any  (or very little) feedback from Microsoft. Don't get me wrong, also a lot of good stuff happens on that site. But two of these "miscommunications" got a bit on my nerve the last few days.
</p>

<p style="text-align: justify;">
  <strong>Prompt pages in Reporting Services</strong>
</p>

<p style="text-align: justify;">
  A few years back I was working with Cognos to develop reports. They have a wonderful concept there, called <em>Prompt Pages</em>. Essentially it is a separate set of report pages which are called before the report is ran. Inside those prompt pages, you can place your various prompts to populate your report parameters. And you have full customizability. You can put everything where you want it, how you want it. Here's a very basic example I found with a quick Google search:
</p>

[<img src="/wp-content/uploads/users/koenverbeeck/ConnectRants/promptpage_example.png?mtime=1375101805" alt="" width="385" height="385" />][1]

<p style="text-align: justify;">
  You can imagine the shock I went through when I first saw the Reporting Services prompts. Anyway, there's been a Connect item logged to request a similar feature: <a style="text-align: justify;" href="http://connect.microsoft.com/SQLServer/feedback/details/545893/allow-ssrs-parameter-panel-to-be-formatted">Allow SSRS Parameter Panel to be formatted</a>. It has 71 votes at the time of writing, which is quite high for a Connect item. It has been logged in 2010 and Microsoft said a feature like this will be availabe in the next version of Reporting Services.  That's SQL Server 2012. I don't see it in this list: <a style="text-align: justify;" href="http://msdn.microsoft.com/en-us/library/ms170438.aspx">What's New (Reporting Services)</a>. I guess they were too busy with Power View. That's cool, new shiny products are nice, but it doesn't mean you need to neglect your already established widely used products (such as Analysis Services MultiDimensional by the way). Especially not when it is a highly requested feature like this one. So please, go to the Connect item and vote it up so that it might be included in SQL Server 2014!
</p>

<p style="text-align: justify;">
  <strong>The PowerPivot vs SSAS Tabular bug</strong>
</p>

<p style="text-align: justify;">
  There's a rather nasty bug that pops up when you try to modify a PowerPivot model in Excel 2013 while you have an SSAS Tabular instance running on the same machine. You get this error:
</p>

<p style="text-align: justify;">
  <em>We couldn't load the Data Model. This may be because the Data Model in the workbook is damaged.</em>
</p>

<p style="text-align: justify;">
  The solution is quite simple: disable the SSAS Tabular service and you can edit your PowerPivot model. But this isn't a real solution, isn't it?
</p>

<p style="text-align: justify;">
  Julie Koesmarno (<a href="http://www.mssqlgirl.com/">blog</a> | <a href="https://twitter.com/MsSQLGirl">twitter</a>) has an entire blog post on the subject: <a href="http://www.mssqlgirl.com/powerpivot-2013-error-data-model-damaged.html">PowerPivot 2013 Error: Data Model Damaged</a>. She also logged the Connect item for this bug: <a href="http://connect.microsoft.com/SQLServer/feedback/details/780949/powerpivot-couldnt-load-data-model-when-ssas-tabular-instance-is-running#tabs">PowerPivot couldn't load Data Model when SSAS Tabular Instance is running</a>. However, Microsoft closed the Connect item, as it is <em>Not Reproducible</em>, although 2 users have marked this bug as reproducible. I'd appreciate it if you went to Connect and voted this bug up, because this one is driving me mad! Especially when I want to load SSAS Tabular data into a PowerPivot model...
</p>

 [1]: /media/users/koenverbeeck/ConnectRants/promptpage_example.png?mtime=1375101805