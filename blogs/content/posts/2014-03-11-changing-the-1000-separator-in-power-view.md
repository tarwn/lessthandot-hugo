---
title: Changing the 1000 separator in Power View
author: Koen Verbeeck
type: post
date: 2014-03-11T14:43:46+00:00
ID: 2500
url: /index.php/datamgmt/dbprogramming/mssqlserver/changing-the-1000-separator-in-power-view/
views:
  - 20781
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Excel Reporting
  - Microsoft SQL Server
  - SSRS
tags:
  - business intelligence
  - formatting
  - power bi
  - power view
  - sql server
  - syndicated

---
<p style="text-align: justify">
  Recently I was designing a simple Power View report on top of a multi-dimensional SSAS cube. Out of the box, one of the tables looked like this:
</p>

<p style="text-align: justify">
  <a style="line-height: 1.5em" href="/wp-content/uploads/2014/03/Koen_Table1.png"><img class="alignnone size-full wp-image-2506" alt="Koen_Table1" src="/wp-content/uploads/2014/03/Koen_Table1.png" width="203" height="94" /></a>
</p>

<p style="text-align: justify">
  <span style="line-height: 1.5em">For readability purposes, a 1000 se</span><span style="line-height: 1.5em">parator is added to the numeric value. However, the comma was used, as is common in the United States. In Belgium (and other countries in Europe), we use the single point as the 1000 separator and the comma as the decimal separator. Of course the business preferred to use the point as the separator. But where do we change this?</span>
</p>

<p style="text-align: justify">
  <b>In Power View itself?</b>
</p>

<p style="text-align: justify">
  When clicking on a cell in the table, you can change the formatting in the Home tab of the ribbon.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/03/Koen_PowerViewFormat.png"><img class="alignnone size-full wp-image-2502" alt="Koen_PowerViewFormat" src="/wp-content/uploads/2014/03/Koen_PowerViewFormat.png" width="280" height="188" /></a>
</p>

<p style="text-align: justify">
  <span style="line-height: 1.5em">First you have to set the type to </span><i style="line-height: 1.5em">Number</i><span style="line-height: 1.5em">, at which point you can select the </span><i style="line-height: 1.5em">comma style</i><span style="line-height: 1.5em">. Unfortunately, this only changes the type to </span><i style="line-height: 1.5em">Accounting</i><span style="line-height: 1.5em"> while using the same separators.</span>
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/03/Koen_Table2.png"><img class="alignnone size-full wp-image-2507" alt="Koen_Table2" src="/wp-content/uploads/2014/03/Koen_Table2.png" width="220" height="93" /></a>
</p>

<p style="text-align: justify">
  <span style="line-height: 1.5em">There is no way to influence what kinds of separators are used. Remarkably, you can set the comma style on, but you cannot turn it off.</span>
</p>

<p style="text-align: justify">
  <b>In the underlying cube?</b>
</p>

<p style="text-align: justify">
  I vaguely remember that Excel from time to time picks up the formatting that is specified in the cube. Maybe Power View does the same. You can set the format string of a measure in the properties pane.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/03/Koen_CubeFormat.png"><img class="alignnone size-full wp-image-2509" alt="Koen_CubeFormat" src="/wp-content/uploads/2014/03/Koen_CubeFormat.png" width="244" height="248" /></a>
</p>

<p style="text-align: justify">
  <span style="line-height: 1.5em">This great article by Valentino Vranken (</span><a style="line-height: 1.5em" href="http://blog.hoegaerden.be/">blog</a><span style="line-height: 1.5em">|</span><a style="line-height: 1.5em" href="https://twitter.com/ValentinoV42">twitter</a><span style="line-height: 1.5em">) details the various options for format strings: </span><a style="line-height: 1.5em" href="http://blog.hoegaerden.be/2013/06/12/formatting-numbers-ssrs/">Formatting Numbers [SSRS]</a><span style="line-height: 1.5em">. Yes, it is about SSRS, but a lot of the concepts are true for other tools as well. However, when we delve deeper into </span><a style="line-height: 1.5em" href="http://msdn.microsoft.com/en-us/library/0c899ak8.aspx#SpecifierTh">custom format strings</a><span style="line-height: 1.5em">, it becomes clear that a format string does not dictate what symbol is used, but that this is determined by the culture. As per MSDN:</span>
</p>

<p style="text-align: justify">
  <i>The NumberGroupSeparator and NumberGroupSizes properties of the current NumberFormatInfo object determine the character used as the number group separator and the size of each number group.</i>
</p>

<p style="text-align: justify">
  So we need to look into the culture used when the Power View report is run. Since we are using Power View in SharePoint, it makes sense to take a look at SharePoint.
</p>

<p style="text-align: justify">
  <b>SharePoint to the rescue...</b>
</p>

<p style="text-align: justify">
  Let's take a look at the site settings.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/03/Koen_SiteSettings.png"><img class="alignnone size-full wp-image-2505" alt="Koen_SiteSettings" src="/wp-content/uploads/2014/03/Koen_SiteSettings.png" width="154" height="276" /></a>
</p>

<p style="text-align: justify">
  <span style="line-height: 1.5em">Regional settings make sense since we need to change locale information.</span>
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/03/Koen_RegionalSettings.png"><img class="alignnone size-full wp-image-2503" alt="Koen_RegionalSettings" src="/wp-content/uploads/2014/03/Koen_RegionalSettings.png" width="154" height="84" /></a>
</p>

<p style="text-align: justify">
  And indeed, we can change to our beloved locale.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/03/Koen_RegionalSettings2.png"><img class="alignnone  wp-image-2504" alt="Koen_RegionalSettings2" src="/wp-content/uploads/2014/03/Koen_RegionalSettings2.png" width="793" height="240" srcset="/wp-content/uploads/2014/03/Koen_RegionalSettings2.png 881w, /wp-content/uploads/2014/03/Koen_RegionalSettings2-300x90.png 300w" sizes="(max-width: 793px) 100vw, 793px" /></a>
</p>

<p style="text-align: justify">
  When opening the report again Power View, we get our desired result:
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/03/Koen_Table3.png"><img class="alignnone size-full wp-image-2508" alt="Koen_Table3" src="/wp-content/uploads/2014/03/Koen_Table3.png" width="222" height="92" /></a>
</p>

<p style="text-align: justify">
  <b>Conclusion</b>
</p>

<p style="text-align: justify">
  The SharePoint regional site settings control how data is displayed in your Power View report.
</p>