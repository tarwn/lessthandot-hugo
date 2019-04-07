---
title: SSRS Properties – Background Image
author: Jes Borland
type: post
date: 2013-01-30T12:26:00+00:00
ID: 1951
excerpt: The purpose of this property is to allow you to show an image in the background of a report, table, list, and more.
url: /index.php/datamgmt/ssrs/ssrs-properties-background-image/
views:
  - 13781
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property:** Background Image

The purpose of this property is to allow you to show an image in the background of a report, table, list, and more.

To access the property, go to Report, Body, Table, or Matrix properties and select Background Image.

The options are Source, Value, MIMEType, and BackgroundRepeat.

<p style="text-align: center;">
  <img style="vertical-align: middle;" src="/wp-content/uploads/users/grrlgeek/Background1.png?mtime=1359555474" alt="" width="276" height="89" />
</p>

Source:

<p style="padding-left: 30px;">
  External – an image that can be accessed via a URL.
</p>

<p style="padding-left: 30px;">
  Embedded – an image that is part of the report definition.
</p>

<p style="padding-left: 30px;">
  Database – an image stored in a database, which is accessed through a dataset.
</p>

Value:

<p style="padding-left: 30px;">
  If External – the image URL.
</p>

<p style="padding-left: 30px;">
  If Embedded – the image name.
</p>

<p style="padding-left: 30px;">
  If Database – the dataset field that contains the image.
</p>

<p style="padding-left: 30px;">
  MIMEType: the image type – bmp, jpeg, gif, png, or x-png.
</p>

Background Repeat:

<p style="padding-left: 30px;">
  Repeat: both horizontal and vertical.
</p>

<p style="padding-left: 30px;">
  RepeatX: horizontal.
</p>

<p style="padding-left: 30px;">
  RepeatY: vertical.
</p>

<p style="padding-left: 30px;">
  Clip: image appears once, anchored in top left corner.
</p>

**Example**: I want to add my company logo as the background to a report.

Choose the Body properties. I select an embedded JPEG, set to clip.

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Background2.png?mtime=1359555474" alt="" />
</p>

This is how the background image appears in Design mode:

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Background3.png?mtime=1359555474" alt="" width="439" height="178" />
</p>

This is how the background image appears in Preview mode:

<p style="text-align: center;">
  <img src="/wp-content/uploads/users/grrlgeek/Background4.png?mtime=1359555474" alt="" width="424" height="295" />
</p>

<p style="text-align: left;">
  Other use cases for this have been putting the words &#8220;DRAFT&#8221; or &#8220;CONFIDENTIAL&#8221; as a watermark on reports.
</p>

**Further Reading:**

[Images (Report Builder and SSRS)][2]

[Add a Background Image (Report Builder and SSRS)][3]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://technet.microsoft.com/en-us/library/dd239394.aspx
 [3]: http://technet.microsoft.com/en-us/library/dd239334.aspx