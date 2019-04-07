---
title: 'Formatting Durations in SSAS: Caveat'
author: Koen Verbeeck
type: post
date: 2014-12-22T12:49:40+00:00
ID: 3125
url: /index.php/webdev/business-intelligence/formatting-durations-in-ssas-caveat/
views:
  - 4146
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSAS
tags:
  - bi
  - mdx
  - ssas
  - syndicated

---
<p style="text-align: justify">
  I recently published an article on <a href="http://www.mssqltips.com/">MSSQLTips.com</a> titled <a href="http://www.mssqltips.com/sqlservertip/3374/format-durations-in-sql-server-analysis-services/">Format Durations in SQL Server Analysis Services</a>. For those who haven’t read it I’ll summarize it quickly: it explains how to create an MDX formula that formats durations into an <em>[hh]:mm:ss</em> format, where [hh] can go over 24 hours. For more detailed information, please check out the article itself.
</p>

<p style="text-align: justify">
  An example:
</p>

[<img class="alignnone wp-image-3127 size-medium" src="/wp-content/uploads/2014/12/FormattedDurations-300x88.jpg" alt="FormattedDurations" width="300" height="88" srcset="/wp-content/uploads/2014/12/FormattedDurations-300x88.jpg 300w, /wp-content/uploads/2014/12/FormattedDurations.jpg 392w" sizes="(max-width: 300px) 100vw, 300px" />][1]

<p style="text-align: justify">
  However, I recently found out at a project that the formula doesn’t really deal well with <em>null</em> values. For instance, when I browse the sample data I get the following result:
</p>

[<img class="alignnone wp-image-3129 size-full" src="/wp-content/uploads/2014/12/NullProblem.jpg" alt="NullProblem" width="700" height="484" srcset="/wp-content/uploads/2014/12/NullProblem.jpg 700w, /wp-content/uploads/2014/12/NullProblem-300x207.jpg 300w" sizes="(max-width: 700px) 100vw, 700px" />][2]

<p style="text-align: justify">
  The returned data set contains (null) values for the <a href="http://msdn.microsoft.com/en-us/library/ms170707.aspx"><em>Unknown</em> member</a>, although empty cells should not be included. If I browse the same set with the unformatted measure, the empty cells are not returned so it the issue is caused with the formatting formula.
</p>

[<img class="alignnone size-medium wp-image-3130" src="/wp-content/uploads/2014/12/Unformatted-264x300.jpg" alt="Unformatted" width="264" height="300" srcset="/wp-content/uploads/2014/12/Unformatted-264x300.jpg 264w, /wp-content/uploads/2014/12/Unformatted.jpg 343w" sizes="(max-width: 264px) 100vw, 264px" />][3]

<p style="text-align: justify">
  My educated guess is that SSAS doesn’t know upfront if the result of the formula will be <em>null</em> or not, so it calculated the measure anyway and displays everything on the grid. The solution is to add an extra check for <em>null</em> values into the formula.
</p>

```vbnet
CREATE MEMBER CURRENTCUBE.[Measures].[DurationFormatted]
 AS   Iif(IsEmpty([Measures].[Duration])
        ,null
        ,   Cstr((Int([Measures].[Duration]) * 24)
        +   CInt(FORMAT(CDate([Measures].[Duration]), "HH")) )
        +   FORMAT(CDate([Measures].[Duration]), ":mm:ss")
        )
,VISIBLE = 1
,ASSOCIATED_MEASURE_GROUP = 'Customer Service';
```<p style="text-align: justify">
  When browsing the cube now, the Unknown member is now ignored due to the explicit check for <em>null</em> values.
</p>

[<img class="alignnone size-medium wp-image-3128" src="/wp-content/uploads/2014/12/IssueFixed-300x296.jpg" alt="IssueFixed" width="300" height="296" srcset="/wp-content/uploads/2014/12/IssueFixed-300x296.jpg 300w, /wp-content/uploads/2014/12/IssueFixed.jpg 396w" sizes="(max-width: 300px) 100vw, 300px" />][4]

 [1]: /wp-content/uploads/2014/12/FormattedDurations.jpg
 [2]: /wp-content/uploads/2014/12/NullProblem.jpg
 [3]: /wp-content/uploads/2014/12/Unformatted.jpg
 [4]: /wp-content/uploads/2014/12/IssueFixed.jpg