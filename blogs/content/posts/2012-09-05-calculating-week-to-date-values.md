---
title: Calculating Week-To-Date values using DAX
author: joshuafennessy
type: post
date: 2012-09-06T01:17:00+00:00
excerpt: 'The expression language of Tabular Mode SSAS – DAX – provides several great time intelligence functions.  One of those it does not provide though a composite function – like DATESMTD or DATESQTD – is one to calculate Week-To-Date summations.  To calcula&hellip;'
url: /index.php/datamgmt/datadesign/calculating-week-to-date-values/
views:
  - 31272
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - SSAS

---
<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">The expression language of Tabular Mode SSAS – DAX – provides several great time intelligence functions.  One of those it does not provide though a composite function – like DATESMTD or DATESQTD – is one to calculate Week-To-Date summations.  To calculate this type of value, a custom expression using the DATESBETWEEN function is needed.</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">In this post, I’ll show you what inputs are needed for the DATESBETWEEN function, how to create a custom start date using DAX, and how to implement this solution in your Tabular Model.  Note that this solution will also work with PowerPivot for Excel, but the examples show will be using SQL Server Data Tools.</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">First, let’s take a look a simple model I have here: </span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"><img src="/wp-content/uploads/users/joshuafennessy/1.png?mtime=1346900843&quot;><img alt=&quot;&quot; src=&quot;/wp-content/uploads/users/joshuafennessy/1.png" alt="" /><br /></span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"> </span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">One <em style="mso-bidi-font-style: normal;">fact</em> table &#8212; Internet Sales – which relates to a single dimension – Dates.</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">This model has the correct relationships created, as well as the Dates table being marked as the <em style="mso-bidi-font-style: normal;">Date Table</em> of the model, and the field <em style="mso-bidi-font-style: normal;">Actual Date</em> flagged as the date field.  With these steps complete I can begin building the pieces I need to create my Week-To-Date calculation.</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">The DATESBETWEEN function requires three parameters;</span>
</p>

<p class="MsoListParagraphCxSpMiddle" style="margin-left: 38.25pt; text-indent: -0.25in;">
  <span style="font-family: verdana,geneva;">·<span style="font-style: normal; font-variant: normal; font-weight: normal; font-size: 7pt; line-height: normal; font-size-adjust: none; font-stretch: normal;"> </span>A list of dates</span>
</p>

<p class="MsoListParagraphCxSpMiddle" style="margin-left: 38.25pt; text-indent: -0.25in;">
  <span style="font-family: verdana,geneva;">·<span style="font-style: normal; font-variant: normal; font-weight: normal; font-size: 7pt; line-height: normal; font-size-adjust: none; font-stretch: normal;"> </span>the start date</span>
</p>

<p class="MsoListParagraphCxSpLast" style="margin-left: 38.25pt; text-indent: -0.25in;">
  <span style="font-family: verdana,geneva;">·<span style="font-style: normal; font-variant: normal; font-weight: normal; font-size: 7pt; line-height: normal; font-size-adjust: none; font-stretch: normal;"> </span> the end date. </span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">In this calculation, I want to use the <em style="mso-bidi-font-style: normal;">week start date</em> in the second parameter, and the currently selected date in the third parameter.  Let’s take a look at how to create the <em style="mso-bidi-font-style: normal;">week start date</em>.</span>
</p>

### <span style="font-family: verdana,geneva;">Creating the Week Start Date</span> {.MsoNormal}

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">In order to create the <em style="mso-bidi-font-style: normal;">week start date</em> I need to first decide what day my week starts on.  For example purposes, let’s pick Tuesday.  To support this, first I need to add a column that contains the <em style="mso-bidi-font-style: normal;">DayNameOfWeek</em> value.  Use the following expression in a calculated field:<br /></span>
</p>

<p class="MsoNormal">
  <span style="font-family: courier new,courier;"> =FORMAT(‘Dates’[ActualDate], “dddd”)</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">Note that my Dates table now looks like this:</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"><img src="/wp-content/uploads/users/joshuafennessy/2.png" alt="" /><br /></span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"> </span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">Next I need to create an expression that will return to me the previous <em style="mso-bidi-font-style: normal;">Tuesday</em> for every date in my table.  Again, I need to create a new column.  The expression to do this is a bit more complicated.  It uses the EARLIER function, which recursively scans the table.</span>
</p>

<p class="MsoNormal">
  <span style="font-family: courier new,courier;"><span style="font-size: 9pt; line-height: 115%;">=CALCULATE(<br /> MAX(Dates[ActualDate]),<br /> FILTER(ALL(Dates), <br /> Dates[DayNameOfWeek]=&#8221;Tuesday&#8221; && <br /> Dates[ActualDate] <= EARLIER(Dates[ActualDate])<br /> )<br /> )</span></span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">This function is using a clever switching of filtering context to return a set of dates less than the current date, and filter that to include only Tuesdays.  Then, with that set handy, the MAX() function is used to return the latest Tuesday.  This equates to the latest Tuesday less than the current date; or in other words, last Tuesday.  My Dates table now looks like this…</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"><img src="/wp-content/uploads/users/joshuafennessy/3.png" alt="" /><br /></span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"> </span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">Now that I have a start date AND an end date.  I can finally write my Week-To-Date calculation.  To do this switch over to the fact table and create a new calculation.</span>
</p>

### <span style="font-family: verdana,geneva;">Creating the Week-To-Date calculated measure</span> {.MsoNormal}

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">In my InternetSales table, I want to create the following calculated measure:</span>
</p>

<p class="MsoNormal">
  <span style="font-family: courier new,courier;">[Sales WTD]:=CALCULATE(</span><br /><span style="font-family: courier new,courier;"> SUM(&#8216;Internet Sales'[SalesAmount]),                         <br /> DATESBETWEEN(          <br /> Dates[ActualDate],<br /> LASTDATE(Dates[WeekStartDate]), <br /> LASTDATE(Dates[ActualDate])</span><span style="font-family: courier new,courier;"><br /> )<br /> );</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">With all of the ground work done in the date table, this expression ends up to be pretty easy!  It’s simply calculating the SUM of [SalesAmount] over a filtered set of DATESBETWEEN() our predefined start date and the end date selected on a report.  Note that in order for this to work, the LASTDATE() function will need to be used.  The DATESBETWEEN() function expects a date value, not a column.  With this calculation created, I can browse the model in Excel and see that it is correctly calculating values.</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"><img src="/wp-content/uploads/users/joshuafennessy/4.png" alt="" /><br /></span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"> </span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"> </span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"> </span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;">If you would like to see this in action, I’ve created a version of this in PowerPivot.  You can download it <a href="https://skydrive.live.com/redir?resid=B33397EE4D528C9A!9620" target="_blank">here</a>.</span>
</p>

<p class="MsoNormal">
  <span style="font-family: verdana,geneva;"> </span>
</p>