---
title: Adding a jQuery Date Picker to Sharepoint
author: ca8msm
type: post
date: 2011-02-02T13:58:00+00:00
excerpt: |
  Here's an example of how you can add jQuery date pickers to input fields within your Sharepoint websites.
  First, so go the Site Settings page and select the Master pages link:
   
url: /index.php/webdev/serverprogramming/adding-a-jquery-date-picker-to-sharepoint/
views:
  - 16786
rp4wp_auto_linked:
  - 1
categories:
  - Javascript
  - Server Programming

---
Here&#8217;s an example of how you can add jQuery date pickers to input fields within your Sharepoint websites.

First, go the Site Settings page and select the Master pages link:

<div class="image_block">
  <a href="/media/blogs/WebDev/SharepointCalendars/SharepointDatePicker1.jpg?mtime=1296660962"><img src="/wp-content/uploads/blogs/WebDev/SharepointCalendars/SharepointDatePicker1.jpg?mtime=1296660962" alt="" width="714" height="346" /></a>
</div>

Next, drop down the default.master page list and select the Edit in Microsoft Office Sharepoint Designer:

<div class="image_block">
  <a href="/media/blogs/WebDev/SharepointCalendars/SharepointDatePicker2.jpg?mtime=1296660982"><img src="/wp-content/uploads/blogs/WebDev/SharepointCalendars/SharepointDatePicker2.jpg?mtime=1296660982" alt="" width="585" height="373" /></a>
</div>

Assuming you have already downloaded and installed the Sharepoint Designer, it will then give you a warning prompt and then open the master page file in the designer:

<div class="image_block">
  <a href="/media/blogs/WebDev/SharepointCalendars/SharepointDatePicker3.jpg?mtime=1296660994"><img src="/wp-content/uploads/blogs/WebDev/SharepointCalendars/SharepointDatePicker3.jpg?mtime=1296660994" alt="" width="661" height="501" /></a>
</div>

We are going to modify this page so start by adding the following code just before the closing </HEAD> tag:

`<link rel="stylesheet" type="text/css" media="all" href="http://ajax.googleapis.com/ajax/libs/jqueryui/1.8.2/themes/base/jquery.ui.core.css" /><br /> <link rel="stylesheet" type="text/css" media="all" href="http://ajax.googleapis.com/ajax/libs/jqueryui/1.8.2/themes/base/jquery.ui.datepicker.css" /><br /> <link rel="stylesheet" type="text/css" media="all" href="http://ajax.googleapis.com/ajax/libs/jqueryui/1.8.2/themes/base/jquery.ui.theme.css" />        <br /> <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.2/jquery.min.js"></script><br /> <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jqueryui/1.8.1/jquery-ui.min.js"></script><br /> <script type="text/javascript"><br /> $(function() {<br /> $('input[id*="DateTimeFieldDate"]').datepicker({ dateFormat: 'dd/mm/yy' });<br /> });<br /> </script>`

This adds a link to 3 external css files for jQuery as well as links to the main jQuery library and the jQuery UI Library. If this is going to be an internal application where the users don&#8217;t have internet access then it may be wise to host all of these files internally (or you may just prefer to do this anyway). It then creates a function that uses a jQuery selector to find all input fields that have the word DateTimeFieldDate in the id and applies the Datepicker to the input with a format of dd/mm/yyyy (you can change this if necessary).

If all goes according to plan, once you save the file and browse to a list with a date column, then you should see the date picker pop up once you click into the input field:

<div class="image_block">
  <a href="/media/blogs/WebDev/SharepointCalendars/SharepointDatePicker4.jpg?mtime=1296661003"><img src="/wp-content/uploads/blogs/WebDev/SharepointCalendars/SharepointDatePicker4.jpg?mtime=1296661003" alt="" width="622" height="393" /></a>
</div>