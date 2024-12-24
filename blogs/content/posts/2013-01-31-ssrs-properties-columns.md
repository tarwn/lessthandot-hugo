---
title: SSRS Properties – Columns
author: Jes Borland
type: post
date: 2013-01-31T12:07:00+00:00
ID: 1952
excerpt: The purpose of this property is to allow you to display more than one column of data on the report.
url: /index.php/datamgmt/ssrs/ssrs-properties-columns/
views:
  - 15686
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: Columns

The purpose of this property is to allow you to display more than one column of data on the report.

To access this property go to Report properties. Choose Columns.

The options are Columns and Column Spacing.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 1.png?mtime=1359640987" alt="" width="286" height="249" />
</p>

Columns: the number of columns the final report will have.

ColumnSpacing: the space between the columns.

**Example**: I want to create a multi-column report to print mailing labels. (I've described this process in more detail [here][2].)

I set Columns to 3 and ColumnSpacing to .03125.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 1.png?mtime=1359640987" alt="" width="286" height="249" />
</p>

The design pane shows 3 columns.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 2.png?mtime=1359640987" alt="" width="1231" height="175" />
</p>

I must set the margins for proper padding. I set left, right, and bottom to 0, and top to .1875.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 3.png?mtime=1359640987" alt="" width="302" height="91" />
</p>

I can then drag an item on to the column. To create mailing labels, I need a list and a textbox. I place the list first, and set the width and height equal to that of the labels I'll be working with.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 4.png?mtime=1359640987" alt="" width="299" height="57" />
</p>

I then add a text box width and height equal to the labels, and adjust the padding.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 5.png?mtime=1359640987" alt="" width="299" height="177" />
</p>

Lastly, I need to set the width and height of the Body so the columns will be properly sized.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 6.png?mtime=1359640987" alt="" width="305" height="227" />
</p>

I add my expression for the labels to the textbox. The design now looks like this.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 7.png?mtime=1359640987" alt="" width="735" height="179" />
</p>

The preview of the report will only show one column.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 8.png?mtime=1359640987" alt="" width="682" height="322" />
</p>

When rendered (in this example, as a PDF), the multiple columns will appear.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/columns 9.png?mtime=1359640987" alt="" width="933" height="240" />
</p>

Creating multi-column reports is easy, for the purposes of labels, nametags, lists, and more.

**Further Reading:**

[Creating Newsletter-Style Reports][3]

[How to: Create a Newsletter-Style Report (Reporting Services)][4]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: /index.php/DataMgmt/ssrs/creating-mailing-labels-in-sql
 [3]: http://msdn.microsoft.com/en-us/library/ms155816(v=sql.100).aspx
 [4]: http://msdn.microsoft.com/en-us/library/ms159107(v=SQL.100).aspx