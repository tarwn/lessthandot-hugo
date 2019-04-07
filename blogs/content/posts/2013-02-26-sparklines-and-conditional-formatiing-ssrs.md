---
title: Sparklines and Conditional Formating in SSRS
author: Sam Vanga
type: post
date: 2013-02-26T11:59:00+00:00
excerpt: |
  When creating a SSRS report, you want to add lines that display trends. You want to show trends for more than one data point. And you want to conditionally format the data point.
  
  In this example, I use Sparklines and a simple expression to create a sparkling report!
url: /index.php/datamgmt/ssrs/sparklines-and-conditional-formatiing-ssrs/
views:
  - 13818
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS
tags:
  - sparklines
  - ssrs

---
When creating a SSRS report, you want to add lines that display trends. You want to show trends for more than one data point. And you want to conditionally format the data point.

In this example, I use Sparklines and a simple expression to create a sparkling report!

Below is how the Sparkline with multiple data points and conditional formatting applied to them will look like. Columns represent sales by month and line represents sales quota by month. Column is in green when sales exceeds the quota for a given month and red when sales fall below the quota.

<div class="image_block">
  <a href="/wp-content/uploads/users/samvanga/ssrs-sparkline.png?mtime=1361623108"><img alt="" src="/wp-content/uploads/users/samvanga/ssrs-sparkline.png?mtime=1361623108" width="348" height="239" /></a>
</div>

### Create dataset:

After opening report server project and creating a data source, create a data set. Right click on shared datasets and choose add new dataset. Following is the query I am using for this example which runs against AdventureWorksDW2008R2 sample database.

<pre>SELECT T.CalendarYear
	, T.CalendarQuarter
	, T.MonthNumberOfYear
	, SUM(S.ExtendedAmount) AS Sales
	, COALESCE(SUM(Q.SalesAmountQuota) / COUNT(SalesAmountQuota), SUM(Q.SalesAmountQuota) / COUNT(SalesAmountQuota)) / 3 AS Quota
	, E.FirstName + ' ' + E.LastName AS Employee
FROM FactResellerSales AS S
LEFT OUTER JOIN dbo.dimDate T ON S.orderdatekey = DateKey
JOIN dbo.DimEmployee E ON S.EmployeeKey = E.EmployeeKey
LEFT OUTER JOIN dbo.FactSalesQuota Q ON S.EmployeeKey = Q.EmployeeKey
	AND T.CalendarYear = Q.CalendarYear
	AND T.CalendarQuarter = Q.CalendarQuarter
WHERE S.EmployeeKey IN (
		SELECT TOP 10 EmployeeKey
		FROM FactResellerSales
		GROUP BY factResellerSales.EmployeeKey
		ORDER BY SUM(ExtendedAmount) DESC
		)
GROUP BY T.CalendarYear
	, T.CalendarQuarter
	, T.MonthNumberOfYear
	, E.FirstName
	, E.LastName
	, E.EmployeeKey
ORDER BY Employee
	, 1</pre>

<div>
  <h3>
    Insert table:
  </h3>
</div>

We want to insert a table grouped on Employee so one row for each employee is shown. Sparklines will later be added to this table.

Drag and drop table report item from the toolbox to report body. Next, drag Employee field from dataset and place it in the details row.

<div class="image_block">
  <a href="/wp-content/uploads/users/samvanga/image_thumb1.png?mtime=1361624083"><img alt="" src="/wp-content/uploads/users/samvanga/image_thumb1.png?mtime=1361624083" width="244" height="64" /></a>
</div>

Right click on the employee text box and insert a row group by choosing row group, group properties. In the group properties window choose Employee from the drop down for group on property.

<div class="image_block">
  <a href="/wp-content/uploads/users/samvanga/ssrs-sparkline-3.png?mtime=1361624083"><img alt="" src="/wp-content/uploads/users/samvanga/ssrs-sparkline-3.png?mtime=1361624083" width="335" height="276" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/samvanga/ssrs-sparkline-4.png?mtime=1361624083"><img alt="" src="/wp-content/uploads/users/samvanga/ssrs-sparkline-4.png?mtime=1361624083" width="423" height="304" /></a>
</div>

### Insert Sparkline:

We will configure a Sparkline with two data points, apply conditional formatting, and add Sparkline to the above table.
  
Drag and drop Sparkline from toolbox on to the report body. Select column as the Sparkline type and click ok.
  
Add sales, Quota from dataset to values and CalendarYear, CalendarQuarter to category groups.

<div class="image_block">
  <a href="/wp-content/uploads/users/samvanga/ssrs-sparkline-5.png?mtime=1361624083"><img alt="" src="/wp-content/uploads/users/samvanga/ssrs-sparkline-5.png?mtime=1361624083" width="424" height="192" /></a>
</div>

Click on Quota from values and choose change Sparkline type. From select Sparkline type page, choose smooth line and click ok.

<div class="image_block">
  <a href="/wp-content/uploads/users/samvanga/ssrs-sparkline-6.png?mtime=1361624084"><img alt="" src="/wp-content/uploads/users/samvanga/ssrs-sparkline-6.png?mtime=1361624084" width="429" height="227" /></a>
</div>

Click on Sales from values and choose series properties.

<div class="image_block">
  <a href="/wp-content/uploads/users/samvanga/ssrs-sparkline-7.png?mtime=1361624084"><img alt="" src="/wp-content/uploads/users/samvanga/ssrs-sparkline-7.png?mtime=1361624084" width="436" height="179" /></a>
</div>

From the series properties page, choose fill and type the following expression
  
<code class="codespan">iif(Fields!Sales.Value<Fields!Quota.Value,“red”,”green”)</code>

Finally, drag and drop Sparkline to a cell next to Employee in the table we created earlier.

<div class="image_block">
  <a href="/wp-content/uploads/users/samvanga/ssrs-sparkline-8.png?mtime=1361624084"><img alt="" src="/wp-content/uploads/users/samvanga/ssrs-sparkline-8.png?mtime=1361624084" width="244" height="56" /></a>
</div>

PS: I originally wrote this post on my [old blog][1]. Since I started blogging on [LessThanDot.com][2] I intend to selectively move content from my previous blog to LTD.

 [1]: http://svangasql.wordpress.com/2011/11/14/how-to-add-sparkline-in-ssrs/
 [2]: http://LessThanDot.com