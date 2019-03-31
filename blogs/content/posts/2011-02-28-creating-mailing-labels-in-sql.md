---
title: Creating Mailing Labels in SQL Server Reporting Services
author: Jes Borland
type: post
date: 2011-02-28T14:52:00+00:00
excerpt: Learn how to create mailing labels using existing information in your database and SQL Server Reporting Services!
url: /index.php/datamgmt/dbprogramming/mssqlserver/creating-mailing-labels-in-sql/
views:
  - 54762
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
  - SSRS
tags:
  - mailing labels
  - sql server reporting services
  - ssrs

---
_SQL Server Reporting Services 2008 R2_ 

Creating mailing labels is a common business need. From sending marketing postcards to prospects, to the annual Christmas cards for customers, labels are frequently used. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRSEnvelopes.JPG?mtime=1298822743"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRSEnvelopes.JPG?mtime=1298822743" width="424" height="283" /></a>
</div>

Are you currently running a report to get your customers mailing information, then using Mail Merge in Microsoft Word to create the labels? Perhaps you are simply exporting the data out of the database. If so, there is an easier way! 

**Step-by-Step** 

The basic steps are to: create a report with multiple columns, add a list, add a textbox, write an expression, and let Reporting Services do the hard work! 

I&#8217;m using Avery 5160 labels as an example. Depending on the labels you have, you may need adjust your columns, width, height and padding. 

1. Create a new, blank report. I&#8217;m using the AdventureWorks database. My query is:

<pre>SELECT        FirstName, LastName, AddressLine1, AddressLine2, City, StateProvinceName, PostalCode
FROM            Sales.vIndividualCustomer</pre>

2. Go to the Report properties.
  
A. Expand Columns, and add the appropriate number of columns. In this example, this will be three.
  
B. You&#8217;ll need to add in the spacing between the labels, too, so they line up correctly when printed. This may be trial and error. In this example, it is .03125 inches.
  
C. Set your Margins. In this example, they are Left 0, Right 0, Top 0.1875, and Bottom 0. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRSLabels1.JPG?mtime=1298823148"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRSLabels1.JPG?mtime=1298823148" width="218" height="508" /></a>
</div>

3. Drag a List onto the report. Adjust the Size to be the Width and Height of your labels, plus a little extra for the strips that go between them. Avery 5160 have a width of 2.8125 and a Height of 1.0625. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRSLabels2.JPG?mtime=1298823148"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRSLabels2.JPG?mtime=1298823148" width="219" height="346" /></a>
</div>

4. Drag a Textbox onto the List.
  
A. Set the Width and Height properties of the textbox. Again, a width of 2.8125 and a height of 1.0625.
  
B. You&#8217;ll also need to adjust the Padding. Remember, this box is slightly larger than the actual labels because of the margins and area between the labels. I set Padding to Left &#8211; 15pt, Right &#8211; 15 pt, Top &#8211; 0 pt, Bottom &#8211; 0 pt. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRSLabels4.JPG?mtime=1298823149"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRSLabels4.JPG?mtime=1298823149" width="218" height="359" /></a>
</div>

5. Set the properties of the Body. Again, a width of 2.8125 and a height of 1.0625 was used. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRSLabels5.JPG?mtime=1298823149"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRSLabels5.JPG?mtime=1298823149" width="217" height="232" /></a>
</div>

6. Go to your textbox and build an expression for your labels. My expression is: 

<code class="codespan">=Fields!FirstName.Value &amp; " " &amp; Fields!LastName.Value &amp; vbcrlf&lt;br />
&amp; Fields!AddressLine1.Value &amp; " " &amp; IIF(NOT(Fields!AddressLine2.Value) = "Nothing", vbcrlf &amp; Fields!AddressLine2.Value, vbcrlf)&lt;br />
&amp; Fields!City.Value &amp; ", " &amp; Fields!StateProvinceName.Value &amp; " " &amp; Fields!PostalCode.Value</code>

7. At this point, Design looks like this: 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRSLabels6.JPG?mtime=1298823150"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRSLabels6.JPG?mtime=1298823150" width="865" height="179" /></a>
</div>

8. Now, go to Preview. You will only see one column of textboxes. This is by design. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRSLabels7.JPG?mtime=1298823150"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRSLabels7.JPG?mtime=1298823150" width="477" height="451" /></a>
</div>

9. Click the Print Layout button and you will see how the labels will print. There are three columns, with the labels nicely laid out! These can now be printed, or exported and printed. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/SSRSLabels8.JPG?mtime=1298823152"><img alt="" src="/wp-content/uploads/users/grrlgeek/SSRSLabels8.JPG?mtime=1298823152" width="891" height="596" /></a>
</div>

**Put It In The Mail!** 

This process is straightforward and can save you time, especially if you are creating labels frequently. Your report can easily be customized with parameters, also, to make it more flexible.