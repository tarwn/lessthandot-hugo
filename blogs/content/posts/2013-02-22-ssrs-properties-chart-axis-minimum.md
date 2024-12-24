---
title: SSRS Properties – Chart Axis Minimum, Maximum, and Interval
author: Jes Borland
type: post
date: 2013-02-22T12:33:00+00:00
ID: 2001
excerpt: The purpose of these properties is to define the minimum and maximum values on a chart axis, and the intervals shown.
url: /index.php/datamgmt/ssrs/ssrs-properties-chart-axis-minimum/
views:
  - 21937
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: Minimum, Maximum, and Interval

The purpose of these properties is to define the minimum and maximum values on a chart axis, and the intervals shown.

To access the properties, select a chart axis and select Minimum, Maximum, or Interval.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/chart min 1.png?mtime=1360936231" alt="" width="274" height="73" />
</p>

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/chart min 2.png?mtime=1360936231" alt="" width="274" height="73" />
</p>

By default, all three are set to Auto. You can set them to any valid range.

**Example**: I have a line chart that shows sales by product category per year. The default Auto settings show from $0 to $20,000,000.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/chart min 3.png?mtime=1360936231" alt="" width="712" height="438" />
</p>

I want to show a range of values from $5,000,000 to $17,500,000, and display an interval of $2,500,000. I set Minimum to 5,000,000, Maximum to 17,500,000, and Interval to 2,500,000.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/chart min 4.png?mtime=1360936231" alt="" width="258" height="77" />
</p>

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/chart min 5.png?mtime=1360936231" alt="" width="260" height="57" />
</p>

When I run the report, the intervals on the vertical axis have been adjusted.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/chart min 6.png?mtime=1360936231" alt="" width="716" height="437" />
</p>

**Further Reading:** 

[Formatting Axis Labels on a Chart (Report Builder and SSRS)][2]

[Specify an Axis Interval (Report Builder and SSRS)][3]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://msdn.microsoft.com/en-us/library/dd239363.aspx
 [3]: http://technet.microsoft.com/en-us/library/dd239317.aspx