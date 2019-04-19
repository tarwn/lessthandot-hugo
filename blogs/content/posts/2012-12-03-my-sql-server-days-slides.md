---
title: My SQL Server Days slides and demos
author: Koen Verbeeck
type: post
date: 2012-12-03T10:16:00+00:00
ID: 1815
excerpt: 'Two weeks ago I had the pleasure of giving a session on the Belgian SQL Server Days. My session was about SSIS and the new features of Change Data Capture (CDC). With new features I mean the new SSIS 2012 components made by Attunity who simplify the man&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/my-sql-server-days-slides/
views:
  - 5162
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - SSIS
tags:
  - cdc
  - change data capture
  - sql server days
  - ssis

---
<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">Two weeks ago I had the pleasure of giving a session on the Belgian <a href="http://www.sqlserverdays.be/2012/" target="_blank">SQL Server Days</a>. My session was about SSIS and the new features of Change Data Capture (CDC). With new features I mean the new SSIS 2012 components made by Attunity who simplify the manageability and usability of SSIS packages working with CDC, because technically CDC hasn't changed since its introduction in SQL Server 2008.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">The session went quite well (still waiting on attendee feedback), aside from some microphone problems who forced me to hold a replacement microphone in my left hand – quite difficult to do demos and use Zoomit – and a transaction that got stuck in SSMS and was stubborn enough not to roll back quickly. But these little setbacks didn't get in the way of the message of the session and they only made myself stronger as a speaker.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">The Belgian SQL Server User Group, <a href="http://sqlug.be/" target="_blank">SQLUG.be</a>, will make all slides available for the attendees, but for those who couldn't attend the event I decided to put them online anyway. The slides can be downloaded from <a href="http://www.slideshare.net/KoenVerbeeck/sqlserverdays2012ssiscdc" target="_blank">SlideShare</a> and the demo material can be found <a href="/media/users/koenverbeeck/SQLServerDays2012_SlidesDemos/SSIS-CDC Demo.zip?mtime=1354310401">here</a>. Basically the demos consist of the following:</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US"> </span>
</p>

  1. <span style="text-align: justify; text-indent: -18pt;" lang="EN-US">Set-up scripts creating some databases and transferring data from AdventureWorksDW2012.</span>
  2. <span style="text-indent: -18pt;" lang="EN-US">A T-SQL script showing CDC functionality as it was introduced in SQL Server 2008.</span>
  3. <span style="text-indent: -18pt;" lang="EN-US">A SSIS package for the initial load.</span>
  4. <span style="text-indent: -18pt;" lang="EN-US">A SSIS package for the incremental load.</span>
  5. <span style="text-indent: -18pt;" lang="EN-US">A SSIS package for the incremental load but with the addition of the __$reprocessing indicator.</span>

<!--[if !supportLists]-->

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">If you have questions about this material, feel free to ask them in the comments.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">For more information on SSIS and CDC, check out the resources I listed at the end of the presentation.</span>
</p>