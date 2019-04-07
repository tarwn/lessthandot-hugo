---
title: 'Reporting Services Error: “The tablix has a detail member with inner members” (And How To Fix It)'
author: Jes Borland
type: post
date: 2011-08-19T11:36:00+00:00
ID: 1302
excerpt: As I work on my Reporting Services 201 session for PASS Summit 2011, I am reminded that there are a few areas I always struggle with. This is how I solve one of them.
url: /index.php/datamgmt/dbprogramming/reporting-services-error-the-tablix/
views:
  - 53255
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - SSRS

---
As I work on my [Reporting Services 201 session for PASS Summit 2011][1], I am reminded that there are a few areas I always struggle with. No matter how many times I add multi-value parameters, I usually forget to change the dataset WHERE clause to IN instead of =. And no matter how many times I create a list and add a table to it, I never remember to delete the detail rows. Every time that happens, a cryptic error appears, and I have to remember how to fix it. 

No more! With this blog post, I have now documented, for my benefit and yours, how to fix this! 

**The Situation** 

I have created a simple report, with a table. I drag a list control on the body, underneath the table. Then, I drag a table on the list. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRS List Design.JPG?mtime=1313722870"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRS List Design.JPG?mtime=1313722870" width="776" height="265" /></a>
</div>

I go to the Preview tab. I see this error: “The tablix ‘tablixname’ has a detail member with inner members. Detail members can only contain static inner members.” 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRS List Preview Error.JPG?mtime=1313722871"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRS List Preview Error.JPG?mtime=1313722871" width="820" height="160" /></a>
</div>

**Say what?** 

A table with detail rows is considered “dynamic” because the number of rows can change. A list is also dynamic, because the number of rows returned can change. A dynamic object can’t be nested inside another. The table must be static, not dynamic. To achieve this, I edit the table so all information is in the header and there are no detail rows. To do so, I right-click the header and choose to Insert Row > Below. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRS List Editing Table 1.jpg?mtime=1313722871"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRS List Editing Table 1.jpg?mtime=1313722871" width="807" height="264" /></a>
</div>

Then, I move the detail information into the new row. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRS List Editing Table 2.jpg?mtime=1313722871"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRS List Editing Table 2.jpg?mtime=1313722871" width="794" height="252" /></a>
</div>

I delete the row group. My list and table look like this. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRS List Design After.JPG?mtime=1313722869"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRS List Design After.JPG?mtime=1313722869" width="783" height="230" /></a>
</div>

Now, when I Preview the report, I see two rows for each item. The first row is the header, with the static titles like “Product Number” and “Name”. The second row is the actual values for that list item. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRS List Preview Fixed.JPG?mtime=1313722873"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRS List Preview Fixed.JPG?mtime=1313722873" width="743" height="232" /></a>
</div>

That’s one more Unsolved Mystery put to rest!

 [1]: http://www.sqlpass.org/summit/2011/Speakers/CallForSpeakers/SessionDetail.aspx?sid=1004