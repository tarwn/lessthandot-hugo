---
title: Here are my SSIS 2012 Data Sources
author: Axel Achten (axel8s)
type: post
date: 2012-03-20T07:48:00+00:00
ID: 1568
excerpt: 'In my previous post I wass looking for the Data Sources and Data Source Views in SSIS 2012 projects. As Carla Sabotta (site|twitter) mentioned in the comments you can change the deployment model of your project so the Data Sources folder will reappear.&hellip;'
url: /index.php/datamgmt/ssis/here-are-my-ssis-2012/
views:
  - 7596
rp4wp_auto_linked:
  - 1
categories:
  - SSIS
tags:
  - data source
  - package deployment model
  - project deployment model
  - ssis 2012

---
In my [previous post][1] I wass looking for the Data Sources and Data Source Views in SSIS 2012 projects. As Carla Sabotta ([site][2]|[twitter][3]) mentioned in the comments you can change the deployment model of your project so the Data Sources folder will reappear. To do this right click the project in Solution Explorer and choose for "Convert to Package Deployment Model":

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DS1.png?mtime=1332236417"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DS1.png?mtime=1332236417" width="524" height="259" /></a>
</div>

After this a warning will appear that you won't be able to convert to the Package Deployment Model if you are using new features. So when I try to convert the package I created in my [previous post][1] I get a nice error report and clicking on the details I get the exact error why my project couldn't be converted:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DS2.png?mtime=1332236430"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DS2.png?mtime=1332236430" width="986" height="477" /></a>
</div>

When I take another (empty) project without Project Connection Managers the conversion succeeds and a Data Sources folder shows up in my Solution Explorer:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/Axel8s/DS3.png?mtime=1332236442"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/Axel8s/DS3.png?mtime=1332236442" width="211" height="137" /></a>
</div>

Also note the explicit mention in the project name that we are working in the Package Deployment Model.
  
So now I can define my Data Sources again like I used to do with SSIS 2005/2008.
  
As a conclusion we can say that there is a change in working with and deploying SSIS 2012 Projects. In the pre 2012 era the packages were handled als fully contained, stand alone packages. Where in SSIS 2012 you can start defining items at the project level and these are inherited by the packages itself.
  
But I still haven't found my Data Source Views.

 [1]: /index.php/DataMgmt/ssis-1/where-are-the-data-sources
 [2]: http://msdn.microsoft.com/en-us/sqlserver/cc511477.aspx
 [3]: https://twitter.com/#!/sabotta