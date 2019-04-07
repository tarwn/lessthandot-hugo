---
title: 'Using multi-select parameters?  Then you might need to do this.'
author: David Forck (thirster42)
type: post
date: 2012-02-20T12:04:00+00:00
excerpt: 'I’ve created SQL Server Reporting Services reports with Multi-Select parameters before.  In fact it’s pretty par for the course for me now.  I recently ran into an issue with one of my reports that I had never seen before.  Every so often I’d get an err&hellip;'
url: /index.php/datamgmt/datadesign/using-multi-select-parameters-then/
views:
  - 5970
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
I’ve created SQL Server Reporting Services reports with Multi-Select parameters before. In fact it’s pretty par for the course for me now. I recently ran into an issue with one of my reports that I had never seen before. Every so often I’d get an error stating “operation cannot run due to current state of the object.” Perplexed, I did some searching. At first I came across this [thread][1] on msdn.

Sri vobilisetti basically says to go to c:program milesmicrosoft sql servermsrs10.mssqlserverreporting servicesReportManager and modify the web config file by adding   <code class="codespan"><add key=”aspnet:MaxHttpCollectionKeys” value=”10000” /&gt; </code> to the appsettings section.

I tried what sri vobilisetti posted, and it fixed my issue. But I still didn’t really understand what caused the issue, so I did some digging into what MaxHttpCollectionKeys was. I eventually found a StackOverflow post that goes into some [detail][2].

So, from my understanding there was a .Net framework update that limited the number of variables that can be sent via a post in a website.

So, basically what that means is if you have a report with multi-select parameters, and you select over a certain number of parameters to run your report on, you’ll get the error “operation cannot run due to current state of the object” because you’ve exceeded the amount of posted data the .Net framework allows.

Not everyone will experience this issue. If your multi-select parameters only have a handful of options, you shouldn&#8217;t ever run into this issue. However, if you have parameters that contain a lot of options, then you&#8217;ll most likely want to change the default value, otherwise you run the risk of an error getting thrown.

 [1]: http://social.msdn.microsoft.com/Forums/en-US/sqlreportingservices/thread/cb6ede72-6ed1-4379-9d3c-847c11b75b32
 [2]: http://stackoverflow.com/questions/8684049/asp-net-ms11-100-how-can-i-change-the-limit-on-the-maximum-number-of-posted-for