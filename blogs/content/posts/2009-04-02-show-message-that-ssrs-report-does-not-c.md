---
title: Show message for SSRS reports that do not return data
author: Ted Krueger (onpnt)
type: post
date: 2009-04-02T15:23:04+00:00
url: /index.php/datamgmt/datadesign/show-message-that-ssrs-report-does-not-c/
views:
  - 25226
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server

---
It is good practice to show the user a message if your report does not return any data. There are a few methods to do this and here is a simple one that can be added to most existing reports that are subscription based.
  
Create a blank report. Add a dataset that returns some data. You can use the following query to create your example…

<pre>select 1 col1,2 col2,3 col3,4 col4,5 col5
union all
select 1 col1,2 col2,3 col3,4 col4,5 col5
union all
select 1 col1,2 col2,3 col3,4 col4,5 col5
union all
select 1 col1,2 col2,3 col3,4 col4,5 col5
union all
select 1 col1,2 col2,3 col3,4 col4,5 col5
union all
select 1 col1,2 col2,3 col3,4 col4,5 col5
union all
select 1 col1,2 col2,3 col3,4 col4,5 col5</pre>

Of course you need a data source to create a report so just create one to a test system or local instance.
  
Drag over a Table and make it the basic ugly SSRS style blue with white fonts. (optional of course 😉 ) One thing before I forget to say is to make sure you use something other than transparent for the details.
  
Next pull the report down a bit so you have some space under the table (if none exists)
  
Drag a textbox over. You should get to a point the report appears like this

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ssrs_ex_1.gif" alt="" title="" width="521" height="114" />
</div>

Right click the table and go into the properties. Click the tab for visibility and tick Expression.
  
In the Expression box type: 

<pre>=iif(Rownumber("{dataset name}")=0, true,false)</pre>

Change {dataset name} to what you named your dataset. Click OK to save the expression and exit properties.
  
In the new textbox type =”No data returned. Report executed on “ & Now
  
Like this…

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ssrs_ex_2.gif" alt="" title="" width="516" height="83" />
</div>

Now you don’t want to see the textbox all the time. The two options you have are to hide it or set the visibility based on something. I like to hide it sense checking for things will obviously take more time.
  
So make sure you have the textbox selected. Right click and select “Send to Back”. Start hitting the up arrow (on the keyboard) so you move the textbox behind the Table. keep doing this until you cannot see the box anymore.
  
Now you can resize the report back to fit the table again.
  
You will end up with the report like this
  
Notice I still have the textbox selected behind the table to show you where it should be placed.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ssrs_ex_3.gif" alt="" title="" width="516" height="83" />
</div>

Hit preview

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ssrs_ex_4.gif" alt="" title="" />
</div>

Go back to your dataset and change the query to this

<pre>declare @tbl table (col1 int,col2 int,col3 int,col4 int,col5 int)
select * from @tbl</pre>

Don’t forget to hit the refresh dataset button and then preview your report again. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt//ssrs_ex_5.gif" alt="" title="" />
</div>

Nice! Now the users know why there is nothing in your report and will refrain from calling you and complaining the hours you put in creating it was futile and it just doesn’t work.