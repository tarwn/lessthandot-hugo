---
title: Report Builder 3.0 – Report Parts
author: Jes Borland
type: post
date: 2012-05-11T11:28:00+00:00
ID: 1625
excerpt: |
  In this post, you’ll learn what report parts are, how to use them, and why you should use them.
  Report Builder 3.0 – The Introduction
  Report Builder 3.0 - Table or Matrix Wizard
  Report Builder 3&hellip;
url: /index.php/datamgmt/dbprogramming/mssqlserver/report-builder-3-0-report/
views:
  - 17732
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - SSRS

---
This is part six of a series about Report Builder 3.0.

  * <a title="Report Builder 3.0 – The Introduction" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-the/" target="_blank">Report Builder 3.0 – The Introduction</a>
  * <a title="Report Builder 3.0 – Table or Matrix Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-table/" target="_blank">Report Builder 3.0 &#8211; Table or Matrix Wizard</a>
  * <a title="Report Builder 3.0 – Chart Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-chart/" target="_blank">Report Builder 3.0 &#8211; Chart Wizard</a>
  * <a title="Report Builder 3.0 – Chart Types, Visualizations, and Properties" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-chart-1/" target="_blank">Report Builder 3.0 – Chart Types, Visualizations, and Properties</a>
  * <a title="Report Builder 3.0 – Map Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-map/" target="_blank">Report Builder 3.0 – Map Wizard</a>

**In this post, you’ll learn what report parts are, how to use them, and why you should use them.**

**What are report parts?** 

Think back to the industrial revolution. What made it truly a revolution? Manufacturers came up with mass-produced pieces. Instead of having to make every screw, wheel, and cog by hand, they were able to use machines to produce many items that were the same. This same concept has been applied to Reporting Services with the introduction of report parts.

Report parts are not a full report. They are items that can be used on a report, published to a server, and re-used in other reports. The items that can be published as parts are:

  * Parameters
  * Tables
  * Matrices
  * Lists
  * Rectangles
  * Charts
  * Gauges
  * Maps
  * Images

If you publish a report part, any dependent parts would also be published. This includes data sources, data set, and parameters.

You can publish from both Report Designer (BIDS, or now SQL Server Data Tools) and Report Builder 3.0. In Report Designer, go to Report > Publish Report Parts. This will only allow you to publish – you can’t add existing parts to a report here.  To publish in Report Builder, follow the steps below.

**Publishing a Report Part** 

When looking at a report in Report Builder 3.0, go to the Report Builder button in the top left corner and select “Publish Report Parts”. A screen that opens will allow you to publish all parts of the report you’ve chosen, or only selected parts.

<p style="text-align: center">
  <img src="/wp-content/uploads/users/grrlgeek/RB3Parts1.JPG?mtime=1336705090" alt="" width="603" height="456" />
</p>

In this report, I only want to deploy Chart8 as a report part. I can click the triangle next to each item to view it, and add a description. You can choose what folder on the report server to publish to by clicking the Browse button. Click the Publish button to send the parts to your report server.

<p style="text-align: center">
  <img src="/wp-content/uploads/users/grrlgeek/RB3Parts2.JPG?mtime=1336705090" alt="" />
</p>

Log in to your report server and navigate to the folder you selected. You will see the report part listed there. If provided, the description will be below the name.

<p style="text-align: center">
  <img style="text-align: center" src="/wp-content/uploads/users/grrlgeek/RB3Parts3.JPG?mtime=1336705090" alt="" />
</p>

**Using a Report Part In Another Report** 

When working with a report, click the Insert tab, then the Report Parts button.

<p style="text-align: center">
  <img src="/wp-content/uploads/users/grrlgeek/RB3Parts4.JPG?mtime=1336705090" alt="" />
</p>

The Report Part Gallery will open.

<p style="text-align: center">
  <img src="/wp-content/uploads/users/grrlgeek/RB3Parts5.JPG?mtime=1336705090" alt="" />
</p>

Double-click the part you wish to use on this report and it will be added to the report. Notice that the associated Data Source and Dataset have also been added.

<p style="text-align: center">
  <img src="/wp-content/uploads/users/grrlgeek/RB3Parts6.JPG?mtime=1336705090" alt="" />
</p>

Yes, publishing and re-using report parts is that easy.

**What benefits do they provide?** 

The two main benefits to using report parts are time savings and consistency &#8211; just like mass-produced machine parts.

Creating a table, chart, or image can be a time-consuming process. There are many details to be considered, such as what data is included, what the layout is, and even color schemes.  If that is done once, and the information is needed again, it will save the organization time. Multiply that by many tables and many reports over a year, and you can see the savings adding up.

Data consistency is also a very important part of report building. When your reports reference “Monthly Sales by Territory”, all definitions of “sales” and “territories” should be the same. If you’re re-using datasets and the items to linked to it, the chance of having inconsistent data is greatly reduced.

Consider using report parts to save time and increase efficiency!