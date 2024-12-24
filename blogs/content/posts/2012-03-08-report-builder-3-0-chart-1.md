---
title: Report Builder 3.0 – Chart Types, Visualizations, and Properties
author: Jes Borland
type: post
date: 2012-03-08T12:07:00+00:00
ID: 1550
excerpt: Charts and other visualizations can be a very powerful and effective way to make your data tell a story.
url: /index.php/datamgmt/dbprogramming/report-builder-3-0-chart-1/
views:
  - 10131
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - SSRS

---
This is part four of a series about Report Builder 3.0.

  * <a title="Report Builder 3.0 – The Introduction" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-the/" target="_blank">Report Builder 3.0 – The Introduction</a>
  * <a title="Report Builder 3.0 – Table or Matrix Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-table/" target="_blank">Report Builder 3.0 – Table or Matrix Wizard</a>
  * <a title="Report Builder 3.0 – Chart Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-chart/" target="_blank">Report Builder 3.0 – Chart Wizard</a>
  * <a title="Report Builder 3.0 – Map Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-map/" target="_blank">Report Builder 3.0 – Map Wizard</a>
  * [Report Builder 3.0 – Report Parts][1]

Charts and other visualizations can be a very powerful and effective way to make your data tell a story. A black and white table listing sales dollars per month can tell an executive which territory has the most sales. A line chart showing the change in sales per month per territory, with a table next to it indicating if the current month's sales are above, in line with, or below last month's sales, is a much more powerful story.

There are many types of charts and other visualizations, and many settings for them. I am going to show you the types of charts available; other visualizations such as data bars, spark lines, and indicators; and properties you can set to make charts pop.

**Types of Charts** 

There are eight categories of charts in Report Builder:

  * Column
  * Line
  * Shape
  * Bar
  * Area
  * Range
  * Scatter
  * Polar

_Column_ charts will show the data as a series of vertical bars. These are great for showing differences between items. There are several variations, such as 3D Columns and 3D Cylinders.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Column.jpg?mtime=1331214270" alt="" />
</p>

<p style="text-align: center">
  Stacked Column
</p>

_Line_ charts show a series of data as a set of points, connected by a line. They're useful for showing how a value moves up or down over time. Variations include smooth and stepped lines.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Line.jpg?mtime=1331214360" alt="" />
</p>

<p style="text-align: center">
  Line
</p>

_Shapes_ included are Pie, Doughnut, Funnel, and Pyramid. These display a value as a percentage of the whole. These are very simple views of the data.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Shape.jpg?mtime=1331214497" alt="" />
</p>

<p style="text-align: center">
  3D Funnel
</p>

_Bar_ charts show the data as horizontal bars. They are a great way to show comparisons between groups of data. Stacked and clustered bars are two of the many variations available.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Bar.jpg?mtime=1331214601" alt="" />
</p>

<p style="text-align: center">
  3D Stacked Horizontal Cylinder
</p>

_Area_ charts show your data as a series of points, with the space below filled in. Variations include smooth and stacked areas.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Area.jpg?mtime=1331214670" alt="" />
</p>

<p style="text-align: center">
  Stacked Area
</p>

_Range_ charts require two values – a low and a high point – to display the range of the data. There are several options to use here, including columns and bars.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Range.jpg?mtime=1331214728" alt="" />
</p>

<p style="text-align: center">
  Range (Image courtesy of <a href="http://technet.microsoft.com/en-us/library/dd207148.aspx">Microsoft TechNet</a>)
</p>

_Scatter_ charts show the data as a set of points. They are best suited to numeric, aggregated data. You can show the data as points or bubbles.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Scatter.jpg?mtime=1331214794" alt="" />
</p>

<p style="text-align: center">
  Scatter (Image courtesy of <a href="http://technet.microsoft.com/en-us/library/dd207047.aspx">Microsoft TechNet</a>)
</p>

_Polar_ charts show data in a circle, with each value having a different length radius. This is a rarely-used type, but can show the differences between categories of data.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Polar.jpg?mtime=1331214861" alt="" />
</p>

<p style="text-align: center">
  Polar (Image courtesy of <a href="http://technet.microsoft.com/en-us/library/dd239337.aspx">Microsoft TechNet</a>)
</p>

You can read more about the types of charts [here][2].

**Expanding Visualizations With More Options** 

There are other visualization options, which are intended to be smaller, simpler views of the data. They are very effective when looked at for an aggregated set of data.

_Data Bar_ 

A _Data Bar_ will show a vertical or horizontal bar for a data point. One bar can represent multiple data points also, like a regular bar chart. Here, I am displaying the number of items per category in a simple data bar format.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Data%20Bar.jpg?mtime=1331214962" alt="" />
</p>

_Sparkline_ 

The _Sparkline_ is one of my favorite tools. They display aggregated data, one point at a time. They are extremely useful for showing trends, and can be configured numerous ways. When you add a sparkline, you can add a Column, Line, Area, Shape, or Range. They are very simplified views of these chart types. This example shows an area sparkline of sales by territory, with a data point for each month.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Sparkline.jpg?mtime=1331215035" alt="" />
</p>

_Indicator_ 

The _Indicators_ included in Report Builder 3.0 are simple gauges that tell you the state of a value at a glance. Examples include directional arrows, flags, circles, starts, and rating bars. This example shows sales by region, and if they are below (red), at (yellow), or above (green) target.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Type%20Indicator.jpg?mtime=1331215113" alt="" />
</p>

**Bringing Them To Life With Properties** 

Within a chart, whatever type you choose, there will be many properties you can set. I'm going to cover a few common ones you can use to get the most out of your reports!

You can access the properties of a chart in two ways. First, you can right-click part of the chart and select the properties option there.

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20Rightclick.jpg?mtime=1331215185" alt="" width="300" height="221" />
</p>

The other option is to look to the right, in the Properties bar. (If you don't see it, go to View and check Properties.)

<p style="text-align: center">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/RB3%20Chart%20PropertiesBar.jpg?mtime=1331215237" alt="" width="400" height="213" />
</p>

_Title_ 

Set a Title for your chart, so the users of the report know what data is being shown to them.

_Legend Properties_ 

A Legend tells users what series on the chart lines up with what data point. There are a couple properties you can set to make this more useful.

<p style="padding-left: 30px">
  Layout – How the items (colors and titles) will appear, such as horizontal or vertical.
</p>

<p style="padding-left: 30px">
  Position – Where the legend will appear in relation to the chart.
</p>

_Chart Area Properties_ 

Area properties will determine the look of the background of the chart. Options vary between chart types, but there are common properties among all of them.

<p style="padding-left: 30px">
  3D – Turn 3D on or off.
</p>

<p style="padding-left: 30px">
  Background color – You can add a color to enhance the background of the chart. This may be useful for drawing attention to a specific chart on a dashboard, for example.
</p>

_Series Properties_ 

The data series in a chart, whether it's a column or a bar or any other sort, can also have their own Series Properties.

<p style="padding-left: 30px">
  Tooltip – Do you want the category name or sales figure to pop up when a user hovers over a bar or a line? Set this property to that value!
</p>

<p style="padding-left: 30px">
  Markers – These are a way to define each data point on a series. You can pick from several shapes and sizes.
</p>

<p style="padding-left: 30px">
  Action –Yyou can have a series go to a subreport, bookmark, or URL. This is a great way to start with high-level information and allow the user to dig into more detail.
</p>

_Vertical Axis Properties_ 

The vertical axis is generally where your values are going to be displayed. It can be helpful to customize this, especially if you're working with numbers or currency.

<p style="padding-left: 30px">
  Axis Options – Range and Interval – Here, you can set the minimum and maximum values that will appear on the axis. You can also choose what interval is shown.
</p>

<p style="padding-left: 30px">
  Labels – Allows you to customize how the labels will appear – or hide them altogether.
</p>

<p style="padding-left: 30px">
  Number – Will let you format the display of the number. I find this especially helpful when dealing with large scales, where I can show numbers in hundreds or thousands.
</p>

_Horizontal Axis Properties_ 

The horizontal axis is generally where your categories and series will be shown. You can customize the appearance of this as well.

<p style="padding-left: 30px">
  Axis Options – You can choose a category or numeric for the horizontal axis type.
</p>

<p style="padding-left: 30px">
  Labels – You can set the labels to offset, rotate, and wrap.
</p>

<p style="padding-left: 30px">
  Number – Again, you can modify how numbers are displayed.
</p>

**Conclusion** 

These are just a small sampling of the many types of visualizations, and the properties associated with them, in Report Builder 3.0. Remember, the information will be more easily understood if it can be visualized, if it tells a story. Use the tools given to you to make your reports the best they can be!

 [1]: /index.php/datamgmt/dbprogramming/mssqlserver/report-builder-3-0-report/ "Report Builder 3.0 – Report Parts"
 [2]: http://technet.microsoft.com/en-us/library/dd220461.aspx