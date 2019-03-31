---
title: 'T-SQL Tuesday #50: Automation, how much of it is the same?'
author: Koen Verbeeck
type: post
date: 2014-01-14T09:41:07+00:00
url: /index.php/datamgmt/dbprogramming/mssqlserver/t-sql-tuesday-50-automation/
views:
  - 16700
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - SSIS
tags:
  - automate
  - biml
  - ssis
  - syndicated
  - tsql2sday

---
<p style="text-align: justify">
  <a href="http://sqlchow.wordpress.com/2014/01/07/t-sql-tuesday-050-automation-how-much-of-it-is-the-same/"><img class="alignleft" alt="TSQL2sday" src="http://blogs.ltd.local/wp-content/uploads/2014/01/TSQL2sday.png" width="120" height="121" /></a>It’s the second Tuesday of the New Year, so here comes the first T-SQL Tuesday of 2014! SQLChow (<a href="http://sqlchow.wordpress.com/">blog</a> | <a href="https://twitter.com/SqlChow">twitter</a>) has the honour to host this jubilee edition of the world famous blog party. The theme of this month is the utterly DBA-related topic of <i>Automation</i>. However, it is pretty easy to give it a BI spin.
</p>

<p style="text-align: justify">
  Even we measly BI developers can automate some parts of the development. I’m talking about BIML, a metadata driven markup language (XML for the people that like acronyms) that can generate SSIS packages like you’ve never seen before. BIML is developed by the nice people at <a href="http://www.varigence.com/">Varigence</a> and an open-source implementation is available in the widely popular Visual Studio add-in <a href="http://bidshelper.codeplex.com/">BIDS Helper</a>, which you can download for free at Codeplex. This post will not explain what BIDS exactly is and how it works; I already did that in an article series at MSSQLTips.com. You can find those <a href="http://www.mssqltips.com/sqlservertip/3094/introduction-to-business-intelligence-markup-language-biml-for-ssis/">here</a>, <a href="http://www.mssqltips.com/sqlservertip/3115/using-biml-to-generate-an-ssis-import-package/">here</a> and <a href="http://www.mssqltips.com/sqlservertip/3124/generate-multiple-ssis-packages-using-biml-and-metadata/">here</a>. Make sure you also check the very excellent resources I link to at the bottom of the articles. The key point you need to remember is that you can use metadata (data about how and where your data is stored) and some scripting with .NET and XML to generate SSIS packages on the fly.
</p>

<p style="text-align: justify">
  The only way a solution with BIDS can work is that you have a lot of the same (see how I referred to the title? Extra bonus points.) Developing a BIML script to generate a single package is just plain crazy. It is much easier to do this directly in BIDS (SSDT/SSDTBI) instead of meddling with those XML tags. However, if you can generate a whole bunch of packages in one single click – for example 20 packages importing flat files or 15 packages doing incremental loads for dimensions – you just saved yourself a whole bunch of time. And that’s where the strength of BIML lies. Take a look at your projects and your SSIS packages and see if you can recognize patterns between them. I bet you can find some. These are opportunities to save a bunch of tedious development effort.
</p>

<p style="text-align: justify">
  To give an example: in my current project, I created a few packages that all do the same thing: read from the source, calculate a hash value, do a lookup on the destination table and determine if a row is an insert or an update (using the hash value). Inserts are directly sent to the destination table, updates are sent to a staging table. Using this staging table a set-based update is issued against the destination table. Finally a SQL statement checks if rows from the destination table are missing in the source (deletes) and a delete flag is marked for those rows. This pattern was exactly the same for each package. Due to time constraints I couldn’t develop a BIML script because it would take me the same amount of time to develop those packages by hand (about 2 days) and there was a hard deadline. However, I will see if I can find time in the following weeks to develop a BIML script anyway, because I know sources will be added in the future. And when they do, I just need to fill in some metadata in a table and the package will be generated with a simple click. This will potentially saves days of work, and I can always take the script to other projects where similar patterns are needed.
</p>

<p style="text-align: justify">
  So BI developers, what are you waiting for? Automate!
</p>