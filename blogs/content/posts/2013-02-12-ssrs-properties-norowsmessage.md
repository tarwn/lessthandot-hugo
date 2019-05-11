---
title: SSRS Properties – NoRowsMessage
author: Jes Borland
type: post
date: 2013-02-12T11:50:00+00:00
ID: 1989
excerpt: The purpose of this property is to display a specific message when no rows are returned to a table, matrix, or list.
url: /index.php/datamgmt/ssrs/ssrs-properties-norowsmessage/
views:
  - 8193
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property:** NoRowsMessage

The purpose of this property is to display a specific message when no rows are returned from a dataset to be displayed in a table, matrix, or list.

To access the property, select the data region and go to NoRowsMessage.

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/NoRows 1.png?mtime=1360676751" alt="" width="249" height="83" />
</p>

**Example:** I have a report that shows sales by territory by year. It has a parameter for the order year. When a user inputs a year that has no orders, it currently displays a blank table. The users have indicated this is confusing and have suggested the report is "broken".

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/NoRows 2.png?mtime=1360676751" alt="" width="494" height="164" />
</p>

I go to NoRowsMessage and enter, "Year entered has no data. Enter another year."

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/NoRows 3.png?mtime=1360676751" alt="" width="352" height="101" />
</p>

When the report is run, if a user enters a year that has no orders, the message is displayed.

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/NoRows 4.png?mtime=1360676751" alt="" width="397" height="157" />
</p>

**Further Reading:**

[Set a No Data Message for a Data Region (Report Builder and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://msdn.microsoft.com/en-us/library/dd220407.aspx