---
title: SSRS Properties – FixedRowHeaders
author: Jes Borland
type: post
date: 2013-02-11T11:59:00+00:00
ID: 1987
excerpt: The purpose of this property is to show row headers as you scroll across a page to view data.
url: /index.php/datamgmt/ssrs/ssrs-properties-fixedrowheaders/
views:
  - 6167
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: FixedRowHeaders

The purpose of this property is to show row headers as you scroll across a page to view data.

To access the property, select the tablix and select FixedRowHeaders.

The options are True or False.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/fixedrow 1.png?mtime=1360590963" alt="" width="248" height="74" />
</p>

**Example:** I have a matrix that displays order counts and totals by year, grouped by sales region. The Territory Name header appears when I am left-most in the rendered report.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/fixedrow 2.png?mtime=1360590963" alt="" width="760" height="251" />
</p>

I go to the report and set FixedRowHeaders to True (and the Background Color of the column with the text boxes to white). When I view the report again, and scroll to the right, I see the row at the left at all times.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/fixedrow 3.png?mtime=1360590963" alt="" width="605" height="307" />
</p>

**Further Reading:**

[Controlling Row and Column Headings (Report Builder 3.0 and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://msdn.microsoft.com/en-us/library/ee240753%28v=sql.105%29.aspx