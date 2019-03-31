---
title: SSRS Properties – Chart Series Tooltip
author: Jes Borland
type: post
date: 2013-02-25T11:36:00+00:00
excerpt: The purpose of this property is to show a specific value on a series in a chart when the user hovers over the data point.
url: /index.php/datamgmt/ssrs/ssrs-properties-chart-series-tooltip/
views:
  - 7914
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: Tooltip

The purpose of this property is to show a specific value on a series in a chart when the user hovers over the data point.

To access the property, select a chart series and go to Tooltip.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/tooltip 1.png?mtime=1361799302" alt="" width="294" height="71" />
</p>

**Example**: I have a line chart that shows sales by category by year. I want the user to be able to see the exact value as a tooltip.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/tooltip 2.png?mtime=1361799302" alt="" width="673" height="402" />
</p>

I go to the Design tab, click on the series to select it, and choose Tooltip and add my expression. I am summing the value, and formatting it as currency.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/tooltip 3.png?mtime=1361799302" alt="" width="358" height="71" />
</p>

When I run the report and hover over a point, the value of that point shows in a yellow tooltip box.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/tooltip 4.png?mtime=1361799302" alt="" width="702" height="427" />
</p>

**Further Reading:** 

[Show ToolTips on a Series (Report Builder and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://technet.microsoft.com/en-us/library/dd220415.aspx