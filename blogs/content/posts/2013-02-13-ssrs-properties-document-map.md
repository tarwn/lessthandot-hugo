---
title: SSRS Properties – Document Map
author: Jes Borland
type: post
date: 2013-02-13T11:52:00+00:00
ID: 1990
excerpt: 'The purpose of this property is to allow you to create a clickable list of values in a pane on the left side of the screen that a user can click on  to quickly reach that value in the report.'
url: /index.php/datamgmt/ssrs/ssrs-properties-document-map/
views:
  - 6750
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: Document Map

The purpose of this property is to allow you to create a clickable list of values in a pane on the left side of the screen that a user can click on to quickly reach that value in the report.

To access the property, select the table or matrix and go to DocumentMapLabel. The options are a field in the dataset, or an expression.

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/docmap 1.png?mtime=1360763297" alt="" width="277" height="127" />
</p>

To set it for a group, select the group in the group pane, go to Group Properties, and select Advanced.

**Example**: I have a report that displays products by subcategory.

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/docmap 2.png?mtime=1360763297" alt="" width="538" height="366" />
</p>

To make it easier to navigate, I want create a Document Map. I go to the Row Groups pane, select the group, and select Group Properties.

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/docmap 3.png?mtime=1360763297" alt="" width="535" height="109" />
</p>

I go to Advanced and set the Document Map to SubcategoryName.

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/docmap 4.png?mtime=1360763297" alt="" width="581" height="472" />
</p>

The report now has a list of subcategories in a pane on the left.

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/docmap 5.png?mtime=1360763297" alt="" width="747" height="381" />
</p>

Selecting a value, such as Jerseys, will automatically take you to the position of that value in the report.

<p style="text-align: center;">
  <img src="/wp-content/uploads/blogs/DataMgmt/docmap 6.png?mtime=1360763297" alt="" width="770" height="402" />
</p>

**Further Reading:** 

[Create a Document Map (Report Builder and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://msdn.microsoft.com/en-us/library/dd239329.aspx