---
title: 'SSRS Properties – Hidden & ToggleItem'
author: Jes Borland
type: post
date: 2013-02-04T12:03:00+00:00
excerpt: The purpose of this property is to have rows or columns automatically expanded or collapsed, with the ability to expand or collapse at any time.
url: /index.php/datamgmt/ssrs/ssrs-properties-hidden-toggleitem/
views:
  - 8325
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property****:** Hidden and ToggleItem

The purpose of this property is to have rows or columns automatically expanded or collapsed, with the ability to expand or collapse at any time, with a toggle item. This is also called drilldown.

To access the properties, select a row or column group in the grouping pane.

The options for Hidden are True or False.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/hidden1.png?mtime=1359986356" alt="" width="309" height="115" />
</p>

The options for ToggleItem are any other valid report item.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/hidden2.png?mtime=1359986356" alt="" width="302" height="281" />
</p>

**Example**: I have a report that shows sales per year by sales territory. This is how it looks when initially run.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/hidden3.png?mtime=1359986356" alt="" width="422" height="311" />
</p>

When run, I want the years and the territories to be collapsed, so it is easier to see all of the values on the report, without scrolling.

Note: always set the toggle properties on the row or column you want hidden or shown, not the item that will toggle it.

I select the detail row first, Order Month

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/hidden4.jpg?mtime=1359986356" alt="" width="384" height="104" />
</p>

Under Properties, I set Hidden to True and ToggleItem to OrderYear1, the text box the year is contained in.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/hidden5.png?mtime=1359986356" alt="" width="302" height="75" />
</p>

When the report is run, the years appear collapsed. Click the + to expand and view details.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/hidden6.png?mtime=1359986357" alt="" width="477" height="228" />
</p>

It is possible to apply this to multiple groups for nesting.

**Further Reading:**

[Add an Expand/Collapse Action to an Item (Report Builder and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://msdn.microsoft.com/en-us/library/dd220405.aspx