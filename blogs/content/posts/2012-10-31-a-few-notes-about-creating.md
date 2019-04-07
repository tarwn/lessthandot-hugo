---
title: A Few Notes About Creating Custom SSMS Reports
author: Jes Borland
type: post
date: 2012-10-31T11:20:00+00:00
ID: 1775
excerpt: A few tips to create custom SQL Server Management Studio reports.
url: /index.php/datamgmt/dbprogramming/a-few-notes-about-creating/
views:
  - 9241
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSRS

---
If you don’t know already, I’m a big fan of SQL Server Reporting Services. It’s an easy-to-use, customizable, scalable, understandable solution for reporting needs in companies of all sizes. I’ve created reports in every version from 2005 to 2012. Recently, I was asked to do something new: create a custom SQL Server Management Studio report. I jumped at the chance. Along the way, I learned a few tips and tricks that I hope will help you!

### Why create and use custom SSMS reports?

SSMS already has some built-in reports. If you right-click an instance name and select Reports > Standard Reports, you’ll see the list. Most reports are performance-based. They show you information about Memory Consumption, Activity – Top Sessions, Performance – Top Queries by Average CPU Time, and more.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/DiskUsageTopTables.JPG?mtime=1351689156" alt="" width="789" height="365" />
</p>

<p style="text-align: center;">
  <em>Disk Usage by Top Tables Report </em>
</p>

The beauty of custom SSMS reports is that you can create a report to show you anything about your instance!  If there are custom metrics you monitor, you can view them here. If you have custom scripts with output that would really pop if they were visual, this is the perfect place to use them. In addition, you don’t need to have SSRS installed on the server you want to run the report against. Custom reports designed in BIDS will run against any SQL Server.

Want to see a custom report I built? [There’s one for Brent Ozar’s sp_blitz script][1]!

### **Compatible Versions of SSMS and Report Designer** 

The first thing I learned is that all versions of SSMS are not compatible with reports built in the matching version of SSRS. SSMS 2005, 2008, and 2008R2 are all built with the SSRS 2005 controls. You must develop reports for those versions in BIDS 2005. I wasn’t thrilled with this requirement.

In SSMS 2012, you can open reports built with SSRS 2012, which is great. You can also open reports built in SSRS 2005. However, reports built in 2012 aren’t backwards compatible.

### **Custom** SSMS Report Limitations

You can add parameters to your report, but they must have default values. User-selected parameters are not allowed in the reports. If you have a report where a user would need to make a selection, it’s best to publish it to an SSRS site.

You can’t embed a subreport. However, you can use linked reports. The .rdl files must reside in the same folder to run them successfully.

You can’t embed hyperlinks to external sources, like a web page. The text will show, but it cannot be clicked on and used as a link.

### Installing and Using Custom SSMS Reports

After the .rdl is created, you want to open it in SSMS. To open the first custom report with an installation of SSMS, you first need to go to C:Users_username_DocumentsSQL Server Management Studio and create a Custom Reports folder. Copy the .rdl file there, and when you select Reports > Custom Reports, you will be able to open custom reports.

Once a report is open, you can export reports, but it’s not obvious. The menu bar above the report gives you the options to refresh and print. If you right-click the report, you can select Export and choose to export as Excel, PDF, or Word. This is useful for sending results to someone, or saving them for future reference.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/DiskUsageTopTablesExport.JPG?mtime=1351689372" alt="" width="900" height="330" />
</p>

<p style="text-align: center;">
  <span style="font-style: italic;">Exporting a Report</span>
</p>

### **Ready, Set, Code!** 

Now you’re ready to go forth and create custom SSMS reports!

 [1]: http://www.brentozar.com/archive/2012/06/making-spblitz-blitzier/