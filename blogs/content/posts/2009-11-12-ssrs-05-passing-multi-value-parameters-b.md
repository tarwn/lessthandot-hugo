---
title: SSRS 05 Passing Multi-Value Parameters Between Reports.
author: David Forck (thirster42)
type: post
date: 2009-11-12T18:19:21+00:00
ID: 626
url: /index.php/datamgmt/datadesign/ssrs-05-passing-multi-value-parameters-b/
views:
  - 18448
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
So today I had some fun with a report. I had to set up a report that had links to some graphs, but I needed to pass the multi-value parameters to the graphs. Awesome. This took me roughly an hour to set up (with googling and messing around trying to get it to work). My eventual set up seems a little less intuitive than I thought it would be, so here I am documenting what I did.

To start off, the main report and graph reports have two multi-select paramaters, Year and Quarter. The range for year is 2006-2012 and quarter is 1-4. These are both set up as strings. The parameters need to be set up like this in every report, otherwise when the values are passed it won’t work properly.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/thirster42/multi-value/screen1.JPG" alt="" title="" width="824" height="608" />
</div>

With the parameters set up, I then set up a text box to point to one of the graph reports.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/thirster42/multi-value/screen2.JPG" alt="" title="" width="468" height="413" />
</div>

I then set up the parameters to pass to the second report. When I first set it up, the parameter defaulted to Value(0). I’m not 100% sure if putting that will work the same or not. Feel free to experiment with it. I removed the (0).

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/thirster42/multi-value/screen3.JPG" alt="" title="" width="424" height="320" />
</div>

Now that the parameters are set to pass to the report, you should be able to run the report and click on the text-box and have the second report open with the correct parameters. Remember, the most important part is setting up the parameters on the secondary reports.