---
title: SSRS Properties – Tablix Filters
author: Jes Borland
type: post
date: 2013-02-05T11:36:00+00:00
ID: 1970
excerpt: The purpose of this property is to allow you to filter the results of a dataset shown in a tablix.
url: /index.php/datamgmt/ssrs/ssrs-properties-tablix-filters/
views:
  - 6216
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: Tablix Filters

The purpose of this property is to allow you to filter the results of a dataset shown in a tablix.

To access the property, right-click a row or column group in the tablix and select Tablix Properties. Select Filters. The options are Expression, Operator, and Value.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/filter 1.png?mtime=1360071212" alt="" width="579" height="596" />
</p>

Expression &#8211; a field from the data set. You can also select the fx button to write an expression.

Operator &#8211; the comparison operator. Options are =, <>, Like, >, >=, <, <=, Top N, Bottom N, Top %, Bottom %, In, Between.

Value &#8211;  the value you want to compare the expression to. This can be a single value, or you can select the fx button to write an expression.

**Example**: I have a report that shows sales per year by sales territory. I want to filter it so only orders placed after 2006 show in totals.

I add the Expression [OrderYear], I set the Operator to >, and I set the Value to 2006.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/filter 2.png?mtime=1360071212" alt="" width="578" height="599" />
</p>

When run, the report only shows totals for orders placed in 2007 and after.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/filter 3.png?mtime=1360071212" alt="" width="483" height="301" />
</p>

There are many ways to filter the data that will be shown on a report. You can filter the T-SQL, through the use of a WHERE clause. You can filter the dataset. You can filter a specific item. Why choose the latter? Two main use cases come to mind. You may have a stored procedure you cannot modify that returns more data than you need. You may also have one dataset for multiple report items, and you want to show different sets of data in each item.

**Further reading:**

[Add a Filter (Report Builder and SSRS)][2]

[Filter Equation Examples (Report Builder and SSRS)][3]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://technet.microsoft.com/en-us/library/ee633648.aspx
 [3]: http://technet.microsoft.com/en-us/library/cc627464.aspx