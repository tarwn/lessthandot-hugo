---
title: SSRS Properties – Tablix Sorting
author: Jes Borland
type: post
date: 2013-02-06T10:48:00+00:00
excerpt: The purpose of this property is to sort the results of a tablix in the order you determine.
url: /index.php/datamgmt/ssrs/ssrs-properties-tablix-sorting/
views:
  - 8265
rp4wp_auto_linked:
  - 1
categories:
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: Tablix Sorting

The purpose of this property is to sort the results of a tablix in the order you determine.

To access the property for a tablix, right-click a row or column and select Tablix Properties. Select Sorting. Click Add.

To select it for a row or column group, go to the group pane, click the down arrow to the right of the group name, and select Group Properties.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/sorting 1.png?mtime=1360154754" alt="" width="580" height="598" />
</p>

Choose what field to sort by or click fx to build an expression. You can sort ascending (A to Z) or descending (Z to A).

**Example**: I have a report that shows sales per year by sales territory. I want to sort it by territory name in ascending order, then by year in descending order.

I go to my first group, which is set on the territory ID, and add sorting on the Name column, in ascending order.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/sorting 2.png?mtime=1360154754" alt="" width="575" height="465" />
</p>

I go to my second group, which is set on OrderYear, and add sorting on the OrderYear column, in descending order.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/sorting 3.png?mtime=1360154754" alt="" width="578" height="472" />
</p>

When I run the report, it is sorted in the order I specified.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/sorting 4.png?mtime=1360154754" alt="" width="476" height="498" />
</p>

This can be useful when datasets return data from stored procedure that isn’t sorted. It can also be useful when you add grouping to the tablix and want to sort the various groups.

**Further reading:**

[Filter, Group, and Sort Data (Report Builder and SSRS)][2]

[Sort Data in a Data Region (Report Builder and SSRS)][3]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://technet.microsoft.com/en-us/library/dd220417.aspx
 [3]: http://technet.microsoft.com/en-us/library/dd255193.aspx