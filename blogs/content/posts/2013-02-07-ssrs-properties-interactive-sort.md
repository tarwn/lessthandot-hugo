---
title: SSRS Properties – Interactive Sort
author: Jes Borland
type: post
date: 2013-02-07T12:39:00+00:00
excerpt: The purpose of this property is to allow you to add interactive sorting capabilities to a textbox in a table or matrix.
url: /index.php/datamgmt/ssrs/ssrs-properties-interactive-sort/
views:
  - 8661
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: Interactive sort

The purpose of this property is to allow you to add interactive sorting capabilities to a textbox in a table or matrix. This allows the user to toggle ascending or descending order for the rows.

To access the property, go to Text Box Properties and select Interactive Sorting.

The options are Enable interactive sorting on this textbox, Sort by, and Apply this sorting to all groups and regions.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Interactive 1.png?mtime=1360247675" alt="" width="573" height="518" />
</p>

Enable interactive sorting allows you to sort the detail rows, or groups.

Sort by is the field in the dataset you wish to sort by.

Apply this sorting allows you to specify that if the user changes the sort order for the column, it will change across other objects within the same dataset or tablix.

**Example:** I have a report that shows me order quantities and sales, by product subcategory and name. Users have requested the ability to sort by all columns.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Interactive 2.png?mtime=1360247675" alt="" width="581" height="229" />
</p>

I go the text box for the Subcategory Name header, right click, and select Text Box Properties.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Interactive 3.png?mtime=1360247675" alt="" width="369" height="231" />
</p>

I go to Interactive Sort. For the first column, I want to sort the detail rows by subcategory name, if someone clicks this box. I set the same property for all other column headers.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Interactive 4.png?mtime=1360247675" alt="" width="577" height="519" />
</p>

When the report is run, there will be up and down arrows in the headers to indicate sorting is available.

I click on the Product Name header, and the results are sorted in ascending order.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Interactive 5.png?mtime=1360247675" alt="" width="568" height="290" />
</p>

If I click twice on the Total Sales header, the results are sorted in descending order.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Interactive 6.png?mtime=1360247800" alt="" width="572" height="212" />
</p>

It is possible to sort by more than one column. I want to sort by subcategory, then product name. I click on Subcategory Name to sort, hold down Shift on the keyboard, and then click on Product Name to sort.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Interactive 7.png?mtime=1360247800" alt="" width="574" height="338" />
</p>

Note: this will only work while the report is rendered; it will not extend to exported reports.

**Further reading:**

[Interactive Sort (Report Builder and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://technet.microsoft.com/en-us/library/dd207011.aspx