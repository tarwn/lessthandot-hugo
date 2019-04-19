---
title: SQL Server 2012 Reporting Services Blueprints – Review
author: Koen Verbeeck
type: post
date: 2013-12-12T12:24:00+00:00
ID: 2207
excerpt: |
   
  
  Recently Packt Publishing released the book SQL Server 2012 Reporting Services Blueprints authored by Marlon Ribunal (blog|twitter) and Mickey Stuewe (blog|twitter). My former colleague Valentino was the technical reviewer for this piece – he wrote&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/reporting-services-blueprints/
views:
  - 22457
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server Admin
  - SSRS
tags:
  - blueprints
  - book
  - reporting services
  - review
  - sql server
  - ssrs

---
<div class="image_block">
  <a href="http://www.packtpub.com/sql-server-2012-reporting-services-blueprints/book?utm_source=mention.com&utm_medium=link&utm_campaign=book_mention.com"><br /><img style="float: left; margin-left: 5px; margin-right: 5px;" src="/wp-content/uploads/users/koenverbeeck/SSRS_blueprints/ssrsblueprints.png?mtime=1386846154" alt="" width="231" height="284" /></a>
</div>

<p style="text-align: justify;">
  Recently <a href="https://www.packtpub.com/">Packt Publishing</a> released the book <a href="http://www.packtpub.com/sql-server-2012-reporting-services-blueprints/book?utm_source=mention.com&utm_medium=link&utm_campaign=book_mention.com">SQL Server 2012 Reporting Services Blueprints</a> authored by Marlon Ribunal (<a href="http://marlonribunal.com/sql-server-express-reporting-data-solutions/">blog</a>|<a href="https://twitter.com/MarlonRibunal">twitter</a>) and Mickey Stuewe (<a href="http://mickeystuewe.com/">blog</a>|<a href="https://twitter.com/SQLMickey">twitter</a>). My former colleague Valentino was the technical reviewer for this piece – he wrote about it <a href="http://blog.hoegaerden.be/2013/11/17/book-sql-server-2012-reporting-services-blueprints/">here</a> – so I was very interested in getting my hands on this book. Luckily Packt was kind enough to provide me with a copy so I could write a review.
</p>

<p style="text-align: justify;">
  <span style="text-align: justify;">The book itself is advertised as a </span><em>step-by-step, task-driven tutorial</em><span style="text-align: justify;">, a hands-on book with some real-life blueprints which doesn't waste time with boring introductions and technical obscurities. In my opinion, the book achieves this goal quite well. At no point I had the feeling I was reading dry technical documentation, but rather practical easy-explained tips on how to use Reporting Services. I also have to admit the reviewers really did a great job: the text reads fluently and I believe I didn't find any spelling mistakes or typos, which are pet peeve of mine (I do hope I didn't make any myself in this blog post). The book is aimed at people new to Reporting Services, so hardcore BI professionals won't get much out of it, but I have to admit I learned a few nuggets here and there.</span>
</p>

<p style="text-align: justify;">
  The book tells the journey of John Kirkland, an <em>awesome accidental DBA</em> for Red Speed Bicycle LLC, a company remarkably similar to AdventureWorks, who also happens to become an <em>accidental</em> report developer. Using this story makes the book relatable and more uplifting than most technical books. The first 4 chapters guide John through the basics of creating reports: how to create data sources and data sets, how to use parameters (with a good piece on multivalued parameters), how to format reports, how to use the different actions (learned something about document maps here) and finally how to implement charts. These chapters are the most valuable for beginners of SSRS and they cover a lot of the basic reporting needs.
</p>

<p style="text-align: justify;">
  The fifth chapter goes into detail about location based reporting and shows how you can use maps in SSRS. The sixth chapter is quite an important one and deals with Analysis Services as data source for the reports. The first sections of this chapter deal with some administrative issues of SSAS, such as for example setting the firewall and finding the correct port. Personally I found this information superfluous and I would have liked more details on how to create reports on top of SSAS. For example, how to create a report using a parent-child hierarchy, how to use distinct count over a many-2-many dimension and so on. I get it from the point of view from John, the hero of the book who is a DBA and might like all that technical information about SSAS, but maybe an additional appendix would have been better. If you're going technical though, why not include more technical deep-dives into managing the SSRS server instead of SSAS?
</p>

<p style="text-align: justify;">
  Chapter 7 deals with deployments and everything that comes after: subscriptions, caching, snapshots et cetera. One minor downside is that it only explains deployment from Visual Studio. There's no explanation on alternatives with scripting. Chapter 8 – the final chapter – is a bit of the “garbage” chapter, as it covers multiple subjects: SharePoint integration, Power View and Power Pivot. What disappointed me most is the very short introduction to Power View which has no screenshots at all. I repeat: no screenshots at all. Power View is Microsoft's answer to competitors like QlickView and it is has beautiful, rich and interactive graphics, it's easy to use and it makes SSRS look pretty bland and boring. But no screenshots. As Power View is officially part of Reporting Services (although it resides in Office and SharePoint) and is another reporting/visualization tool, I'd rather have a full chapter on Power View and less info on the SSAS DBA and the geo reporting stuff.
</p>

<p style="text-align: justify;">
  The book closes with two appendixes: a short one on SSRS best practices and one on using replication to create a separate reporting database.
</p>

<p style="text-align: justify;">
  <strong>Conclusion</strong>
</p>

<p style="text-align: justify;">
  I really enjoyed the book and I will recommend it to people new to Reporting Services to get up to speed quickly with this tool. It has great examples that you can follow along using provided code snippets. The only downside – but this is my personal opinion – is the lack of Power View goodness. Don't let this stop you though on adding this title to your library!
</p>