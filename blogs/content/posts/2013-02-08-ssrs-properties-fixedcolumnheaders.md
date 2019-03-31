---
title: SSRS Properties – FixedColumnHeaders
author: Jes Borland
type: post
date: 2013-02-08T11:44:00+00:00
excerpt: The purpose of this property is to show column headers as you scroll down a page to view data.
url: /index.php/datamgmt/ssrs/ssrs-properties-fixedcolumnheaders/
views:
  - 4641
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: FixedColumnHeaders

The purpose of this property is to keep column headers visible as you scroll down a page to view data.

To access the property, select the tablix and select FixedColumnHeaders.

The options are True or False.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/fixedcolumn 1.png?mtime=1360330912" alt="" width="286" height="86" />
</p>

**Example:** I have a matrix that display order counts and totals by year, grouped by sales region. The Year header appears when I am at the top of the rendered report.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/fixedcolumn 2.png?mtime=1360330912" alt="" width="761" height="254" />
</p>

I go to the tablix properties and set FixedColumnHeaders to True (and the Background Color to white). When I view the report again, and scroll down, I see the header at the top at all times.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/fixedcolumn 3.png?mtime=1360330912" alt="" width="768" height="236" />
</p>

**Further Reading:**

[Controlling Row and Column Headings (Report Builder 3.0 and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://msdn.microsoft.com/en-us/library/ee240753%28v=sql.105%29.aspx