---
title: Doing a Full Outer Join in Power Query
author: Koen Verbeeck
type: post
date: 2015-07-02T11:55:34+00:00
ID: 3450
url: /index.php/webdev/business-intelligence/doing-a-full-outer-join-in-power-query/
views:
  - 26693
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
tags:
  - join
  - power bi
  - power query
  - syndicated

---
<p style="text-align: justify">
  A while back a give a session at the <a href="http://www.element61.be/">element61 </a>Microsoft Business Analytics Day, a free event where the capabilities of  the Microsoft BI platform is demonstrated alongside client testimonials. I gave a session called &#8220;Drilling across Analysis Services cubes using Power Query&#8221;, which talked about how you can use Power Query to do a <em>drill across</em> analysis over multiple SSAS cubes. Drilling across combines facts from multiple fact tables through their conformed dimensions, something that is not possible if your data is split up against multiple cubes. I even wrote an article about it: <a href="http://www.element61.be/e/resourc-detail.asp?ResourceId=876">Drilling across Analysis Services cubes using Power Query</a>.
</p>

<p style="text-align: justify">
  Basically the solution is to create a query for each fact table and then combine them using the Merge transformation in Power Query, which basically is a Left or Inner Join. At the end of the session, I got the question if it&#8217;s possible to do a Full Outer Join instead, to which my initial response was &#8220;Ehrmmm&#8230;&#8221;. It&#8217;s not possible through the user interface, so I gave the advice to create a query that contains the cross join of the dimensions you want to use, then left join all the fact tables against that query and at the end filter out any rows where all the facts are empty. This advice still stands for people who do not want to write a single line of code in Power Query. However, when reading Chris Webbs <a href="http://www.amazon.com/Power-Query-BI-Excel/dp/1430266910/ref=sr_1_1?s=books&ie=UTF8&qid=1435817503&sr=1-1">excellent book about Power Query</a> and more specifically the chapter about M, I realized there is a more elegant solution out there.
</p>

<p style="text-align: justify">
  In this blog post, I&#8217;ll use the following data as a source:
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2015/07/MergeSource.jpg"><img class="alignnone size-full wp-image-3455" src="/wp-content/uploads/2015/07/MergeSource.jpg" alt="MergeSource" width="478" height="135" srcset="/wp-content/uploads/2015/07/MergeSource.jpg 478w, /wp-content/uploads/2015/07/MergeSource-300x84.jpg 300w" sizes="(max-width: 478px) 100vw, 478px" /></a>
</p>

<p style="text-align: justify">
  You start out just as usual, by creating two queries on top of the these tables and then merging them together in the user interface.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2015/07/MergeGUI.jpg"><img class="alignnone wp-image-3456" src="/wp-content/uploads/2015/07/MergeGUI-e1435818852971.jpg" alt="MergeGUI" width="800" height="670" srcset="/wp-content/uploads/2015/07/MergeGUI-e1435818852971.jpg 871w, /wp-content/uploads/2015/07/MergeGUI-e1435818852971-300x251.jpg 300w" sizes="(max-width: 800px) 100vw, 800px" /></a>
</p>

<p style="text-align: justify">
  After clicking OK, you get the following result:
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2015/07/MergeResult.jpg"><img class="alignnone wp-image-3457 size-full" src="/wp-content/uploads/2015/07/MergeResult-e1435818919233.jpg" alt="MergeResult" width="757" height="175" srcset="/wp-content/uploads/2015/07/MergeResult-e1435818919233.jpg 757w, /wp-content/uploads/2015/07/MergeResult-e1435818919233-300x69.jpg 300w" sizes="(max-width: 757px) 100vw, 757px" /></a>
</p>

<p style="text-align: justify">
  The import part here is the M formula in the formula bar. There is actually room for a final, optional parameter: <strong>JoinKind</strong>. If we simply add JoinKind.FullOuter, we get our desired result.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2015/07/MergeResult_FullOuter.jpg"><img class="alignnone size-full wp-image-3458" src="/wp-content/uploads/2015/07/MergeResult_FullOuter.jpg" alt="MergeResult_FullOuter" width="902" height="187" srcset="/wp-content/uploads/2015/07/MergeResult_FullOuter.jpg 902w, /wp-content/uploads/2015/07/MergeResult_FullOuter-300x62.jpg 300w" sizes="(max-width: 902px) 100vw, 902px" /></a>
</p>

<p style="text-align: justify">
  Of course, we still have to expand the NewColumn column to retrieve the Category and the Value from the right input.
</p>

<p style="text-align: justify">
   <a href="/wp-content/uploads/2015/07/MergeResult_Expand.jpg"><img class="alignnone size-full wp-image-3459" src="/wp-content/uploads/2015/07/MergeResult_Expand.jpg" alt="MergeResult_Expand" width="1050" height="188" srcset="/wp-content/uploads/2015/07/MergeResult_Expand.jpg 1050w, /wp-content/uploads/2015/07/MergeResult_Expand-300x53.jpg 300w, /wp-content/uploads/2015/07/MergeResult_Expand-1024x183.jpg 1024w" sizes="(max-width: 1050px) 100vw, 1050px" /></a>
</p>

<p style="text-align: justify">
  And finally we have to do some cosmetics to get a decent presentable result:
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2015/07/MergeFinalResult.jpg"><img class="alignnone size-full wp-image-3460" src="/wp-content/uploads/2015/07/MergeFinalResult.jpg" alt="MergeFinalResult" width="354" height="141" srcset="/wp-content/uploads/2015/07/MergeFinalResult.jpg 354w, /wp-content/uploads/2015/07/MergeFinalResult-300x119.jpg 300w" sizes="(max-width: 354px) 100vw, 354px" /></a>
</p>

<p style="text-align: justify">
  I added the following transformations:
</p>

<ul style="text-align: justify">
  <li>
    Renaming columns
  </li>
  <li>
    Adding a custom column taking the left category, unless it is null, then the right category is taken
  </li>
  <li>
    Removing the old category columns
  </li>
  <li>
    Moving columns around
  </li>
</ul>

<p style="text-align: justify">
  <strong>Conclusion</strong>
</p>

<p style="text-align: justify">
  Doing a Full Outer Join in Power Query is really straight forward and you don&#8217;t have to be an M guru to adapt the code.
</p>