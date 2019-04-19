---
title: 'SSRS Nugget: displaying placeholders when no rows are returned'
author: Koen Verbeeck
type: post
date: 2014-05-15T12:59:04+00:00
ID: 2625
url: /index.php/datamgmt/dbprogramming/mssqlserver/ssrs-nugget-displaying-placeholders-when-no-rows-are-returned/
views:
  - 20964
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Microsoft SQL Server
  - SSRS
tags:
  - business intelligence
  - reporting services
  - sql server
  - ssrs
  - syndicated

---
<p style="text-align: justify">
  <span style="line-height: 1.5em">A few days ago there was an interesting question on the forum:</span>
</p>

> How can I get None in each cell of a table in the report if no rows are returned? If I use “No rows message” property, header is not displaying on the report but I would like to get headers along with “NONE” in each column if no rows returned.

<p style="text-align: justify">
  So the “No rows message” property was not what the OP wanted. He still wanted to display the table with the header, but just a line with the value “None” for every cell. To test it out, I created a simple report pulling back some data from AdventureWorks. I used the following query:
</p>

sql
SELECT        ProductCategoryAlternateKey, EnglishProductCategoryName
FROM            DimProductCategory;
```
<p style="text-align: justify">
  Not exactly rocket science. This gives me the following tablix (I spent an insanely amount of time on the layout):
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/05/reportwithdata.png"><img class="alignnone size-full wp-image-2630" alt="reportwithdata" src="/wp-content/uploads/2014/05/reportwithdata.png" width="424" height="146" srcset="/wp-content/uploads/2014/05/reportwithdata.png 424w, /wp-content/uploads/2014/05/reportwithdata-300x103.png 300w" sizes="(max-width: 424px) 100vw, 424px" /></a>
</p>

<p style="text-align: justify">
  What if the source query suddenly stops returning rows? I added WHERE 1 = 0 at the end of the query and I ran the report again (to remove caching, delete the .data cache file in the project folder).
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/05/reportonlyheader.png"><img class="alignnone size-full wp-image-2629" alt="reportonlyheader" src="/wp-content/uploads/2014/05/reportonlyheader.png" width="450" height="71" srcset="/wp-content/uploads/2014/05/reportonlyheader.png 450w, /wp-content/uploads/2014/05/reportonlyheader-300x47.png 300w" sizes="(max-width: 450px) 100vw, 450px" /></a>
</p>

<p style="text-align: justify">
  Only the header is returned. Now we want to add a row with only “None” for values. To do this, we simply hardcode such a row into the table and only show it when necessary.
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/05/report_incorrect1.png"><img class="alignnone size-full wp-image-2628" alt="report_incorrect1" src="/wp-content/uploads/2014/05/report_incorrect1.png" width="448" height="103" srcset="/wp-content/uploads/2014/05/report_incorrect1.png 448w, /wp-content/uploads/2014/05/report_incorrect1-300x68.png 300w" sizes="(max-width: 448px) 100vw, 448px" /></a>
</p>

<p style="text-align: justify">
  On the row visibility property, I used the following expression:
</p>

```vb
=iif(CountRows() = 0,False,True)
```
<p style="text-align: justify">
  You can find more info on CountRows <a href="http://technet.microsoft.com/en-us/library/ms156330(v=sql.100).aspx">here</a>. However, when I ran the report the extra row didn't show up. Maybe I needed to add a scope as a parameter to the CountRows function? So I changed the expression to this, with <em>TablixTest</em> being the name of the Tablix:
</p>

```vb
=iif(CountRows(“TablixTest”) = 0,False,True)
```
<p style="text-align: justify">
  The line still didn't show up. I changed the scope to <i>Detail</i> (in the hope it would count the detail lines), but no luck. To be honest, I don't know much about scopes in SSRS. Then I had an idea: I added the extra row to the details of the tablix. What if I added it outside that group?
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/05/addrow_outsidegroup.png"><img class="alignnone size-full wp-image-2632" alt="addrow_outsidegroup" src="/wp-content/uploads/2014/05/addrow_outsidegroup.png" width="447" height="219" srcset="/wp-content/uploads/2014/05/addrow_outsidegroup.png 447w, /wp-content/uploads/2014/05/addrow_outsidegroup-300x146.png 300w" sizes="(max-width: 447px) 100vw, 447px" /></a>
</p>

<p style="text-align: justify">
  I used the original expression again – without any scope – and when I ran the report, the row finally showed up!
</p>

<p style="text-align: justify">
  <a href="/wp-content/uploads/2014/05/reportwithoutdata.png"><img class="alignnone size-full wp-image-2631" alt="reportwithoutdata" src="/wp-content/uploads/2014/05/reportwithoutdata.png" width="430" height="77" srcset="/wp-content/uploads/2014/05/reportwithoutdata.png 430w, /wp-content/uploads/2014/05/reportwithoutdata-300x53.png 300w" sizes="(max-width: 430px) 100vw, 430px" /></a>
</p>

<p style="text-align: justify">
  When the report is run again with rows returned, the line disappears as it should be.
</p>