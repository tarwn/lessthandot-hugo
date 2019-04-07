---
title: Sparklines and Conditional Formatiing in SSRS
author: Sam Vanga
type: post
date: -001-11-30T00:00:00+00:00
ID: 2008
excerpt: 'When creating a SSRS report, you want to add lines that display trends. Sparklines introduced with SQL Server 2008 R2 allow you to do this easily. You want to show trends for more than one data point. For example, actual sales and target amount. And you&hellip;'
draft: true
url: /?p=2008
categories:
  - Business Intelligence
  - SSRS

---
When creating a SSRS report, you want to add lines that display trends. Sparklines introduced with SQL Server 2008 R2 allow you to do this easily. You want to show trends for more than one data point. For example, actual sales and target amount. And you want to conditionally format the data point, show colors green or red depending upon if sales exceeds target.

Below is how the Sparkline with multiple data points and conditional formatting applied to them will look like. Columns represent sales by month and line represents sales quota by month. Column is in green when sales exceeds the quota for a given month and red when sales fall below the quota.