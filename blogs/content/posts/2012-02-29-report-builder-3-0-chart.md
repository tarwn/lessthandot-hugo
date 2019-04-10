---
title: Report Builder 3.0 – Chart Wizard
author: Jes Borland
type: post
date: 2012-02-29T13:54:00+00:00
ID: 1544
excerpt: SQL Server Report Builder 3.0 has a Chart Wizard that allows you to build colorful, graphic charts in a few simple steps.
url: /index.php/datamgmt/dbprogramming/report-builder-3-0-chart/
views:
  - 12857
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - SSRS

---
This is part three of a series about Report Builder 3.0.

  * <a title="Report Builder 3.0 – The Introduction" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-the/" target="_blank">Report Builder 3.0 – The Introduction</a>
  * <a title="Report Builder 3.0 – Table or Matrix Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-table/" target="_blank">Report Builder 3.0 – Table or Matrix Wizard</a>
  * <a title="Report Builder 3.0 – Table or Matrix Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-table/" target="_blank">Report Builder 3.0 – Chart Types, Visualizations, and Properties</a>
  * <a title="Report Builder 3.0 – Map Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-map/" target="_blank">Report Builder 3.0 – Map Wizard</a>
  * <a title="Report Builder 3.0 – Report Parts" href="/index.php/datamgmt/dbprogramming/mssqlserver/report-builder-3-0-report/" target="_blank">Report Builder 3.0 – Report Parts</a>

In this post, I'll cover the Chart Wizard, which allows you to build colorful, graphic charts in a few simple steps.

The Chart Wizard can be accessed in two ways. It will appear on the splash screen when you open Report Builder. It can also be found under File > New.

**The Wizard** 

![][1]

First, I have to choose a dataset. I can pick a shared dataset, or create a new one. I choose to create one.

![][2]

After I select my dataset, I am taken to the Design a query screen. (More in-depth information on this screen can be found in [Report Builder 3.0 – Table or Matrix Wizard][3].)

I build my query using the Production.Product, Production.ProductCategory and Production.ProductSubcategory tables.

![][4]

Then, I have to Choose a chart type. I select Pie. (Mmm…pie!)

![][5]

Now I can “Arrange chart fields”. Charts will require at least one field in “Categories”, and at least one aggregated field in “Values”.

Categories are how the values will be divided up on the horizontal axis.

Values are the points at which the values will appear along the vertical axis.

Adding a series will break the values up by the specified value.

A further explanation of these can be found here <http://technet.microsoft.com/en-us/library/dd239351.aspx>.

![][6]

On the Choose a Style screen, you can choose the color palette for your chart. I choose Mahogany.

When I click Finish, I’m at the main Report Builder screen.

![][7]

**Viewing the Report** 

I click Run in the top right to preview my results.

![][8]

This is very basic. Very, very basic. A future post will go more in-depth on chart settings. For now, though, try a few simple things to make this look better.

Add a report title.

Right-click the pie chart and select “Show Data Labels” to have the values appear in the pie slice.

Right-click the square around the pie, select “Chart Area Properties”, and choose “3D”.

![][9]

Small changes like that will make your report into this:

![][10]

The Chart Wizard is a quick, simple way to build an effective report. Many times, data that is represented graphically can be more easily understood.

 [1]: /wp-content/uploads/users/grrlgeek/RB3ChartWiz1.JPG?mtime=1330529209
 [2]: /wp-content/uploads/users/grrlgeek/RB3ChartWiz2.JPG?mtime=1330529210
 [3]: /index.php/DataMgmt/ssrs/report-builder-3-0-table
 [4]: /wp-content/uploads/users/grrlgeek/RB3ChartWiz3.JPG?mtime=1330529210
 [5]: /wp-content/uploads/users/grrlgeek/RB3ChartWiz4-1.JPG?mtime=1330529211
 [6]: /wp-content/uploads/users/grrlgeek/RB3ChartWiz5.JPG?mtime=1330529212
 [7]: /wp-content/uploads/users/grrlgeek/RB3ChartWiz6.JPG?mtime=1330529212
 [8]: /wp-content/uploads/users/grrlgeek/RB3ChartWiz7.JPG?mtime=1330529213
 [9]: /wp-content/uploads/users/grrlgeek/RB3ChartWiz8.JPG?mtime=1330529214
 [10]: /wp-content/uploads/users/grrlgeek/RB3ChartWiz9.JPG?mtime=1330529215