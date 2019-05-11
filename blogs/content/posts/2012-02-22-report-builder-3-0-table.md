---
title: Report Builder 3.0 – Table or Matrix Wizard
author: Jes Borland
type: post
date: 2012-02-22T12:42:00+00:00
ID: 1532
excerpt: SQL Server Report Builder 3.0 has a built-in Table or Matrix Wizard. It will walk you through all the necessary building blocks for a table or matrix, and generate a report layout for you.
url: /index.php/datamgmt/dbprogramming/report-builder-3-0-table/
views:
  - 20149
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - SSRS

---
This is part two of a series about Report Builder 3.0.

  * <a title="Report Builder 3.0 – The Introduction" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-the/" target="_blank">Report Builder 3.0 – The Introduction</a>
  * <a title="Report Builder 3.0 – Chart Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-chart/" target="_blank">Report Builder 3.0 – Chart Wizard</a>
  * <a title="Report Builder 3.0 – Chart Types, Visualizations, and Properties" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-chart-1/" target="_blank">Report Builder 3.0 – Chart Types, Visualizations, and Properties</a>
  * <a title="Report Builder 3.0 – Map Wizard" href="/index.php/datamgmt/dbprogramming/report-builder-3-0-map/" target="_blank">Report Builder 3.0 – Map Wizard</a>
  * <a title="Report Builder 3.0 – Report Parts" href="/index.php/datamgmt/dbprogramming/mssqlserver/report-builder-3-0-report/" target="_blank">Report Builder 3.0 – Report Parts</a>

In this post, I'm going to introduce you to one the Table or Matrix Wizard.

<p style="text-align: center">
  <img style="vertical-align: middle" src="/wp-content/uploads/users/grrlgeek/Wizard.jpg?mtime=1329921629" alt="" width="196" height="225" />
</p>

SQL Server Report Builder 3.0 has a built-in Table or Matrix Wizard. It will walk you through all the necessary building blocks for a table or matrix, and generate a report layout for you. This is great if you're not comfortable with starting a report from scratch, or want to quickly create a report.

The Table or Matrix Wizard can be accessed in two ways. It will appear on the splash screen when you open Report Builder. It can also be found under File > New.

**The Wizard** 

![][1]

First, choose a dataset. I select "Create a dataset".

![][2]

I created a new dataset to connect to AdventureWorks2008R2.

![][3]

The Design a Query screen can be intimidating at first glance. There is a lot of information in one place. It's easy to understand once it's broken down, though.

On the left, the Database View column shows the database, sorted by schema. Expand a schema to see its tables, views and stored procedures. Expand a table or view and click on the box in front of a column to add it to the report. Expand stored procedures, and check one to use it in the report.

The right pane has three sections: Selected Fields, Relationships and Applied Filters.

Selected Fields shows you the fields that you've selected for use in this query. In the top right, use the X to delete a column, or the arrows to reorder your selections. You can use Group and Aggregate to select a field to aggregate and other fields to group by.

Relationships shows the relationships between tables. You can use Auto Detect to find relationships based on primary and foreign keys defined in the database. You can also edit an existing relationship, or create one yourself.

Applied Filters allows you filter the results of the query.

<p style="padding-left: 30px">
  <em>Hint:</em> Prefer writing T-SQL? Click Edit As Text to do so.
</p>

<p style="padding-left: 30px">
  <em>Hint:</em> Already have a query written and saved as a .sql or .rdl file? Click Import to use it.
</p>

![][4]

Click Run Query to run it and see you results.

![][5]

The next screen, Arrange Fields, allows you to add groups to your table or matrix. A table can have row groups. A matrix can have row and column groups. Values is the fields from your dataset that will appear on the report. You can apply aggregations to these. Click on a field and drag it to the appropriate box.

Hint: adding a numeric field to Values will automatically Sum it. Click the drop-down arrow and uncheck Sum if you do not want aggregation.

In my example, I do not want any grouping, so I only add Values.

![][6]

Choose the Layout shows you how the report will look. Don't like the order of the columns or rows? You can go back and rearrange if needed.

![][7]

Choose A Style will apply a set of colors and fonts to the report. (Anyone that has used the wizard in Report Designer will be familiar with these.) If you want no colors, choose Generic.

![][8]

The report is created and displayed on the main screen. It's now able to be edited further, or run.

![][9]

Clicking Run will run the report and display the results. You can view, print and export from here.

Click Save to save the .rdl.

**Deploying** 

Is that perfect, brand new, shiny report ready to be turned loose in the business? It's time to deploy it!

In the bottom left corner, you should see "No current report server." Click Connect. Enter the Report Server URL.

Go to the File menu and click Publish Report Parts.

![][10]

I chose Publish all report parts with default settings.

I navigate to my report server. I have a new folder listed, Report Parts.

![][11]

In this folder, there is one item: the tablix that I created in this report.

![][12]

If you choose Review and Modify, it lets you select which parts of the report to publish. You can add a description, and also choose which folder to publish them to.

![][13]

The Table or Matrix Wizard is a powerful tool to help you quickly design reports in Report Builder.

Next, we'll look at the Chart Wizard!

 [1]: /wp-content/uploads/users/grrlgeek/RB3TableWiz1.JPG?mtime=1329271885
 [2]: /wp-content/uploads/users/grrlgeek/RB3TableWiz2.JPG?mtime=1329271885
 [3]: /wp-content/uploads/users/grrlgeek/RB3TableWiz3.JPG?mtime=1329272175
 [4]: /wp-content/uploads/users/grrlgeek/RB3TableWiz4.JPG?mtime=1329272176
 [5]: /wp-content/uploads/users/grrlgeek/RB3TableWiz5.JPG?mtime=1329271887
 [6]: /wp-content/uploads/users/grrlgeek/RB3TableWiz6.JPG?mtime=1329272354
 [7]: /wp-content/uploads/users/grrlgeek/RB3TableWizStyle.jpg?mtime=1329272675
 [8]: /wp-content/uploads/users/grrlgeek/RB3TableWiz7.JPG?mtime=1329271960
 [9]: /wp-content/uploads/users/grrlgeek/RB3TableWiz8.JPG?mtime=1329271961
 [10]: /wp-content/uploads/users/grrlgeek/RB3TableWiz9.JPG?mtime=1329273169
 [11]: /wp-content/uploads/users/grrlgeek/RB3TableWiz10.JPG?mtime=1329271962
 [12]: /wp-content/uploads/users/grrlgeek/RB3TableWiz11.JPG?mtime=1329271962
 [13]: /wp-content/uploads/users/grrlgeek/RB3TableWiz12.JPG?mtime=1329271962