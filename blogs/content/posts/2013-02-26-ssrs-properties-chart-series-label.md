---
title: SSRS Properties – Chart Series Label Visibility
author: Jes Borland
type: post
date: 2013-02-26T11:06:00+00:00
ID: 2014
excerpt: |
  This blog is part of my series Making Data Tell a Story With SSRS Properties.
  Property: Label - Visible
  The purpose of this property is to show the values of data points in the series on a chart.
  To access the property, go to the chart series propert&hellip;
url: /index.php/datamgmt/ssrs/ssrs-properties-chart-series-label/
views:
  - 8821
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: Label &#8211; Visible

The purpose of this property is to show the values of data points in the series on a chart.

To access the property, go to the chart series properties, expand Label, and go to Visible.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/label visible 1.png?mtime=1361883851" alt="" width="276" height="344" />
</p>

**Example**: I have a chart that shows sales by year, with a series for each product category.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/label visible 2.png?mtime=1361883851" alt="" width="673" height="402" />
</p>

Because there are few data points on the chart, I want each value to show a label of the exact value.

I go to the Design tab, select the series on the chart, expand Label, and set Visible to True.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/label visible 3.png?mtime=1361883851" alt="" width="634" height="396" />
</p>

I want this to look more readable. I set Font Size to 8, Format to “c” (currency), and Position to Top.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/label visible 4.png?mtime=1361883851" alt="" width="271" height="335" />
</p>

The chart is much more readable now.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/label visible 5.png?mtime=1361883851" alt="" width="683" height="398" />
</p>

**Further Reading:** 

[Position Labels in a Chart (Report Builder and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://technet.microsoft.com/en-us/library/dd220469.aspx