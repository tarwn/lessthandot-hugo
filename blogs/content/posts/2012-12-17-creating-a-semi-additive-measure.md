---
title: Creating a Semi-additive Measure over a Parent-child Hierarchy
author: Koen Verbeeck
type: post
date: 2012-12-17T11:11:00+00:00
ID: 1844
excerpt: 'Originally this blog post would have had the title “Disabling aggregations over a parent-child hierarchy”, but I thought it could create confusion with the Aggregation Design concept in Analysis Services (SSAS), which is something completely different.&hellip;'
url: /index.php/datamgmt/dbprogramming/mssqlserver/creating-a-semi-additive-measure/
views:
  - 48638
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - SSAS
tags:
  - mdx
  - parent-child hierarchy
  - semi-additive
  - ssas

---
<p style="text-align: justify;">
  Originally this blog post would have had the title <em>“Disabling aggregations over a parent-child hierarchy”</em>, but I thought it could create confusion with the Aggregation Design concept in Analysis Services (SSAS), which is something completely different.
</p>

<p style="text-align: justify;">
  What I want to describe in this blog post is a method forcing a parent-child hierarchy in SSAS to show the parent’s own data value, instead of the sum of the aggregated values of its children. Why would I want to do such a thing? A client of mine once asked me to create a cube on some financial data. However, the data was delivered by a 3rd party and could not be changed. This means that a parent-child hierarchy – for example the chart of accounts on a Profit&Loss report – should display the values from the database, not vpalues calculated or aggregated by SSAS. In most cases the values would have been the same, but sometimes it wouldn’t, hence the need to create an alternative solution.
</p>

<p style="text-align: justify;">
  Basically I want to create some sort of semi-additive measure, which aggregates values for all dimensions, except the parent-child hierarchy. In this blog post I use sample data regarding some of the bloggers on this site:
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/sampledata.PNG?mtime=1355177204"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/sampledata.PNG?mtime=1355177204" alt="" width="682" height="343" /></a>
</div>

<p style="text-align: justify;">
  The bloggers themselves have the following parent-child hierarchy. (Disclaimer: this is just a fabricated sample. It doesn’t represent any actual hierarchy 🙂 )
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/Hierarchy_All.PNG?mtime=1355177164"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/Hierarchy_All.PNG?mtime=1355177164" alt="" width="188" height="151" /></a>
</div>

<p style="text-align: justify;">
  The following star schema is used to build the cube:
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/DSV.PNG?mtime=1355177069"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/DSV.PNG?mtime=1355177069" alt="" width="413" height="522" /></a>
</div>

<span style="text-align: justify;">With the default values configured in the SSAS cube, we get this pivot table:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/Excel_AggTrue_VisibleData_NoCalc.PNG?mtime=1355177144"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/Excel_AggTrue_VisibleData_NoCalc.PNG?mtime=1355177144" alt="" width="248" height="207" /></a>
</div>

<span style="text-align: justify;">We can see SSAS has aggregated the values of the children to the parents, which is incorrect in our case. For example, at the time of writing SQLDenis had written 482 blog posts, not 501. Every parent is also repeated: once with the calculated aggregated value and once with its actual value. This tends to be very confusing for end users, so we want to avoid this.</span>

<p style="text-align: justify;">
  <strong>IsAggregatable</strong>
</p>

<p style="text-align: justify;">
  This is a dimension attribute property with a very promising name. However, the only visible effect it has is supplying an <em>(All)</em> level on the attribute hierarchy if this property is set to <em>True</em>.
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/IsAggregatable.PNG?mtime=1355177185"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/IsAggregatable.PNG?mtime=1355177185" alt="" width="423" height="193" /></a>
</div>

<span style="text-align: justify;">If we set this property to False, we get the following hierarchy:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/Hierarchy_NoAll.PNG?mtime=1355177173"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/Hierarchy_NoAll.PNG?mtime=1355177173" alt="" width="177" height="134" /></a>
</div>

<span style="text-align: justify;">The </span>_All_ <span style="text-align: justify;">member is gone, Chrissie is at the top, but the results are still the same when we browse the cube:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/browsecube.PNG?mtime=1355177054"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/browsecube.PNG?mtime=1355177054" alt="" width="277" height="164" /></a>
</div>

<span style="font-weight: bold; text-align: justify;">MembersWithData</span>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">This parent-child hierarchy property specifies how members with data are treated. We have two options: <em>NonLeafDataVisible</em> (the default) and <em>NonLeafDataHidden</em>.</span>
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/MembersWithData.PNG?mtime=1355177194"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/MembersWithData.PNG?mtime=1355177194" alt="" width="359" height="192" /></a>
</div>

<span style="text-align: justify;">The default specifies if a parent has data, the parent is repeated as a child of its own. The child value will have the actual data, while the parent value will show the aggregated value of all its children. The </span>_NonLeafDataHidden_ <span style="text-align: justify;">setting will cause the parent-child hierarchy to show only the actual children of a parent and the parent itself with the aggregated value. This gives the following result in our sample:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/Excel_AggTrue_HiddenData_NoCalc.PNG?mtime=1355177121"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/Excel_AggTrue_HiddenData_NoCalc.PNG?mtime=1355177121" alt="" width="251" height="164" /></a>
</div>

<span style="text-align: justify;">This is even worse, as it’s not clear how a parent gets its value. For example, blogger onpnt has a value of 406, but grrlgeek has the value 108. The pivot table does not show how the value 406 is calculated, again leading to much confusion.</span>

<p class="MsoNormal" style="text-align: justify;">
  <strong><span lang="EN-US">Defining our semi-additive measure</span></strong><span lang="EN-US"> </span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">SSAS has the possibility to </span><a href="http://msdn.microsoft.com/en-US/library/ms175356(v=sql.105).aspx" target="_blank"><span lang="EN-US">define semi-additive measures</span></a><span lang="EN-US"> – at least in the Enterprise edition for SQL Server 2008 R2 – but unfortunately this doesn’t work for our case: only the time dimension can be taken into account for semi-additive behavior.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">So we need create our own calculated semi-additive measure. The following MDX does the trick:</span>
</p>

```MDX
([Blogger].[Blogger].CURRENTMEMBER.DATAMEMBER,[Measures].[Blog Count])
```
<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">This calculated measure is the tuple of the normal additive measure </span><em>Blog </em>Count with the current data member of the parent-child hierarchy <em>Blogger</em>. This forces the cube to take the actual value of a member in the hierarchy instead of calculating an aggregate. We now get the following results:
</p>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/Excel_AggTrue_VisibleData_Calc.PNG?mtime=1355177136"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/Excel_AggTrue_VisibleData_Calc.PNG?mtime=1355177136" alt="" width="346" height="206" /></a>
</div>

<span style="text-align: justify;">We are on the right track: each parent displays its own actual value. However, the parents are duplicated. Remember the </span>_NonLeafDataHidden_ <span style="text-align: justify;">property? Let’s use this to get rid of those extra child members:</span>

<div class="image_block" style="text-align: center;">
  <div class="image_block">
    <a href="/media/users/koenverbeeck/DisableAggregations/Excel_AggTrue_HiddenData_Calc.PNG?mtime=1355177107"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/Excel_AggTrue_HiddenData_Calc.PNG?mtime=1355177107" alt="" width="334" height="161" /></a>
  </div>
</div>

<span style="text-align: justify;">Now every member of the parent-child hierarchy displays its own data. The semi-additive behavior of this solution is confirmed when browsing other dimensions:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/browseotherdimensions.PNG?mtime=1355177061"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/browseotherdimensions.PNG?mtime=1355177061" alt="" width="390" height="154" /></a>
</div>

<span style="text-align: justify;">There’s no difference between our calculated measure and the normal measure defined in the measure group. We can mix all the dimensions together and the values are still displayed correctly:</span>

<div class="image_block" style="text-align: center;">
  <a href="/media/users/koenverbeeck/DisableAggregations/Excel_Final.PNG?mtime=1355177155"><img src="/wp-content/uploads/users/koenverbeeck/DisableAggregations/Excel_Final.PNG?mtime=1355177155" alt="" width="599" height="216" /></a>
</div>

<span style="font-weight: bold; text-align: justify;">Conclusion</span>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">We can define semi-additive behavior on a parent-child hierarchy with a fairly easy calculated measure. The downside is that you end up with two measures in your cube: a correct one and a not entirely correct one from an end user point of view. This can be solved by using perspectives, if applicable.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">Note: the sample project can be downloaded <a href="/media/users/koenverbeeck/DisableAggregations/CreatingSemiAdditiveMeasureOnPC.zip?mtime=1355178036" target="_blank">here</a>.</span>
</p>