---
title: Report Builder 3.0 – The Introduction
author: Jes Borland
type: post
date: 2012-02-15T15:13:00+00:00
ID: 1531
excerpt: 'I’ve been building reports with SQL Server Reporting Services for a few years. Until recently, I exclusively used Report Designer, which let me build reports in Business Intelligence Development Studio, a Visual Studio environment. I decided to step outside my comfort zone and learn a new tool - Report Builder 3.0.'
url: /index.php/datamgmt/dbprogramming/report-builder-3-0-the/
views:
  - 17941
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - SSRS

---
This is part one of a series about Report Builder 3.0.

  * <a title="Report Builder 3.0 – Table or Matrix Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-table/" target="_blank">Report Builder 3.0 &#8211; Table or Matrix Wizard</a>
  * <a title="Report Builder 3.0 – Chart Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-chart/" target="_blank">Report Builder 3.0 &#8211; Chart Wizard</a>
  * <a title="Report Builder 3.0 – Chart Types, Visualizations, and Properties" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-chart-1/" target="_blank">Report Builder 3.0 – Chart Types, Visualizations, and Properties</a>
  * <a title="Report Builder 3.0 – Map Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-map/" target="_blank">Report Builder 3.0 – Map Wizard</a>
  * <a title="Report Builder 3.0 – Report Parts" href="/index.php/datamgmt/dbprogramming/mssqlserver/report-builder-3-0-report/" target="_blank">Report Builder 3.0 – Report Parts</a>

I’ve been building reports with SQL Server Reporting Services for a few years. Until recently, I exclusively used Report Designer, which let me build reports in Business Intelligence Development Studio, a Visual Studio environment. This was warm and fuzzy and comfortable to this (gasp) former programmer.

<p style="text-align: center">
  <img title="Yes, @onpnt, I stole your image!" src="/wp-content/uploads/users/grrlgeek/RB3.0 Intro Programmer.gif" alt="" width="277" height="282" />
</p>

<span style="text-align: center">I decided to step outside my comfort zone and learn a new tool – Report Builder 3.0.</span>

<p class="MsoNormal">
  <strong>What is it? </strong>
</p>

<p class="MsoNormal">
  Both Report Designer and Report Builder are authoring tools for SQL Server Reporting Services. Report Designer is a very programmer-friendly environment, since it’s built on the Visual Studio shell. Report Builder is a very business-user-friendly environment, as it mimics the ribbon and layout of Microsoft Office that is familiar to so many people.
</p>

<p class="MsoNormal">
  <strong>Licensing </strong>
</p>

<p class="MsoNormal">
  You can download the Report Builder 3.0 client front end <a href="http://www.microsoft.com/download/en/details.aspx?id=6116">here</a>. Publishing reports requires a licensed version of SQL Server 2008 R2.
</p>

<p class="MsoNormal">
  <strong>Data Sources </strong>
</p>

<p class="MsoNormal">
  What data sources can I connect to? You can connect to any valid SSRS source: SQL Server, SSAS, Oracle, SAPBW, and more.
</p>

<p class="MsoNormal">
  <strong>Supported SSRS Versions </strong>
</p>

<p class="MsoNormal">
  Report Builder 3.0 generates RDLs that are for SQL Server 2008R2. Thus, you can’t publish these reports to SSRS 2008 or SSRS 2005 instances. (Of course, you can use those as data sources.)
</p>

<p class="MsoNormal">
  <strong>First Look </strong>
</p>

<p class="MsoNormal">
  Let’s open Report Builder 3.0 and take it for a test drive.
</p>

<p class="MsoNormal">
  Initially, the Getting Started box will pop up.
</p>

<p class="MsoNormal">
  You can start a New Report four ways:
</p>

<p class="MsoNormal" style="text-indent: .5in">
  Table or Matrix Wizard
</p>

<p class="MsoNormal" style="text-indent: .5in">
  Chart Wizard
</p>

<p class="MsoNormal" style="text-indent: .5in">
  Map Wizard
</p>

<p class="MsoNormal" style="text-indent: .5in">
  Blank Report
</p>

<p class="MsoNormal">
  New Dataset allows you to create a dataset to share among multiple reports.
</p>

Open allows you to open a saved .rdl.

Recent allows you to open .rdl files you've recently worked on.

<p class="MsoNormal">
  <img src="/wp-content/uploads/users/grrlgeek/RB3%20Start.JPG?mtime=1329269464" alt="" />
</p>

<p class="MsoNormal">
  If you don’t select any of those options, you’re taken to the main screen. This is a combination of the Office ribbons and the Visual Studio sidebars. I found it very easy to start using right away, as I’m familiar with both programs.
</p>

<p class="MsoNormal">
  <strong>The Main Screen </strong>
</p>

<p class="MsoNormal">
  The application can be broken into four main areas: Top Menus, Report Data Sidebar, Properties Sidebar, and Groups.
</p>

<p class="MsoNormal">
  <img src="/wp-content/uploads/users/grrlgeek/RB3MainScreen.JPG" alt="" />
</p>

<p class="MsoNormal">
  <strong>Top Menus </strong>
</p>

<p class="MsoNormal">
  <img src="/wp-content/uploads/users/grrlgeek/RB3Main Home.JPG" alt="" />
</p>

<p class="MsoNormal">
  <strong>Home</strong> provides a lot of basic options you would see in any Office product, such as font, paragraph, and number formatting options. The most important button here is the Run button, which will execute the report and show you the results.
</p>

<p class="MsoNormal">
  <img src="/wp-content/uploads/users/grrlgeek/RB3Main Insert.JPG" alt="" />
</p>

<p class="MsoNormal">
  <strong>Insert</strong> is where the magic happens. This is the toolbar you will use to add objects to your reports. (This is similar to the Toolbox sidebar in Report Designer.) The table, matrix, and list are used as data regions. You can add many different visualizations, such as charts, gauges, and indicators. You also have the ability to add a header or footer, and link to a subreport.
</p>

<p class="MsoNormal">
  When working on a report, to add an item, click on the item on the Insert bar, and then click on the spot on the report body you want it to appear.
</p>

<p class="MsoNormal">
  <img src="/wp-content/uploads/users/grrlgeek/RB3Main View.JPG" alt="" />
</p>

<p class="MsoNormal">
  <strong>View</strong> will allow you to show or hide different sidebars on the screen.
</p>

<p class="MsoNormal">
  <strong>Report Data </strong>
</p>

<p class="MsoNormal">
  <img src="/wp-content/uploads/users/grrlgeek/RB3ReportData.JPG" alt="" />
</p>

<p class="MsoNormal">
  This sidebar is where you can edit the “guts” of the report. You can add and edit parameters, images, data sources, and datasets. To add any of these, click New in the top left, or right-click the item and select Add. To edit an item, double-click it and the corresponding Properties window will open (except for Images).
</p>

<p class="MsoNormal">
  <strong>Groups </strong>
</p>

<p class="MsoNormal">
  <img src="/wp-content/uploads/users/grrlgeek/RB3Main%20Groups.JPG?mtime=1329270156" alt="" />
</p>

<p class="MsoNormal">
  This window allows you to manage row and column groups. You can add, remove, and edit by clicking the small down arrows on the right side.
</p>

<p class="MsoNormal">
  <strong>Properties </strong>
</p>

<p class="MsoNormal">
  <img src="/wp-content/uploads/users/grrlgeek/RB3 Main Properties.JPG" alt="" />
</p>

<p class="MsoNormal">
  The Properties window is a great resource. When you have any item on the report selected, whether that is the body, an individual textbox in a tablix, or a chart, you can set properties here. It’s a quick way to view and set many common properties.
</p>

<p class="MsoNormal">
  <strong>Tour Complete </strong>
</p>

<p class="MsoNormal">
  Those are the main components of Report Builder 3.0. Thank you for keeping your hands and feet inside the ride at all times. Next, we’ll look at how to build a table or matrix using the built-in wizard (which, compared to the Report Designer wizard, is far cooler).
</p>

<p class="MsoNormal">
  <strong>Resources</strong>
</p>

<p class="MsoNormal">
  Here are additional resources you can begin reading to get more familiar with Report Builder 3.0.
</p>

<p class="MsoNormal">
  <a href="http://msdn.microsoft.com/en-us/library/ms159253.aspx">Designing Reports in Report Designer and Report Builder 3.0 (SSRS)</a>
</p>

<p class="MsoNormal">
  <a href="http://msdn.microsoft.com/en-us/library/dd207008.aspx">Report Builder 3.0 (MSDN)</a>
</p>

<p class="MsoNormal">
  <a href="http://technet.microsoft.com/en-us/ff657833.aspx">Report Builder TechNet Portal</a>
</p>

<p class="MsoNormal">
  <a href="http://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0CCsQFjAA&url=http%3A%2F%2Fdownload.microsoft.com%2Fdownload%2F7%2FF%2FD%2F7FDAA75C-1273-4DFE-8EC6-D9699C3EE47F%2FSQL_Server_2008_R2_Report_Builder_3_0FAQs.docx&ei=ew4vT_PFBOnd0QH6uvDoCg&usg=AFQjCNE4Ezq7rKhK_3vX7UpnOAOyAov-IA">SQL Server 2008 R2 Report Builder 3.0 FAQs (Word doc)</a>
</p>