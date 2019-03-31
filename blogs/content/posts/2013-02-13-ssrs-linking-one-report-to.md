---
title: SSRS – Linking one report to another report
author: Kevin Conan
type: post
date: 2013-02-13T18:09:00+00:00
excerpt: When creating reports, I try to keep them simple, clean and with as little information as possible.
url: /index.php/datamgmt/ssrs/ssrs-linking-one-report-to/
views:
  - 13874
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS
tags:
  - sql
  - ssrs

---
When creating reports, I try to keep them simple, clean and with as little information as possible. What I mean by as little information as possible is that I try to keep the information summarized so that the value of the report isn’t lost in a sea of details. However, for some reports you still need those details to back up the summary and to allow for digging deeper into issues.

What I have found works well is to add links in the summary report to the detail report. This approach allows us to pass in parameters to it and still limit the detail report to just the information that is needed. 

To create a link in a report, you go to the Text Box Properties of the text box that you want to be the link, choose Action on the left side and then the “Go to report” radio button option. 

<div class="image_block">
  <a href="/wp-content/uploads/users/kconan/SSRS - action.JPG?mtime=1360785989"><img alt="" src="/wp-content/uploads/users/kconan/SSRS - action.JPG?mtime=1360785989" width="580" height="525" /></a>
</div>

If you are using Visual Studio, the dropdown list under “Specify a report” will be populated with included reports in your project. You can also use the expression builder to key in the name of the report you are linking to. The name you key in is the published report name and the .rdl file name. This screen also allows you to specify what parameters you want to pass into the detail report.

When you publish the summary and detail reports, if you publish them to the same directory then the summary report will automatically find the detail report when the link is clicked.