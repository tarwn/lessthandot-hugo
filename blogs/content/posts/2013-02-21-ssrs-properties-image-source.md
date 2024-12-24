---
title: SSRS Properties – Image Source
author: Jes Borland
type: post
date: 2013-02-21T15:23:00+00:00
ID: 2000
excerpt: The purpose of this property is to specify where a report image is stored.
url: /index.php/datamgmt/ssrs/ssrs-properties-image-source/
views:
  - 6817
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSRS

---
This blog is part of my series [Making Data Tell a Story With SSRS Properties][1].

**Property**: Source

The purpose of this property is to specify where a report image is stored.

To access the property, select an image and go to Source. The options are External, Embedded, and Database.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/imagesource 1.png?mtime=1360935786" alt="" width="282" height="89" />
</p>

Options:

  * External – an image stored on a server. Specify a URL or path.
  * Embedded – an image uploaded to and embedded in the report RDL.
  * Database – an image stored in a database table.

**Example**: I want to include the company logo in the header of a sales report.

I add a report header and drag an image control on to it. I select External as the source. I put the URL to the image in Use this image.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/imagesource 2.png?mtime=1360935786" alt="" width="580" height="519" />
</p>

When I run the report, the image is in the header.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/imagesource 3.png?mtime=1360935787" alt="" width="425" height="385" />
</p>

If I want to have the image embedded with the report, I need to add it. I go to the Report Data pane and select New > Image.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/imagesource 4.png?mtime=1360935787" alt="" width="207" height="150" />
</p>

I navigate to the location the image is saved and select it. The image is now available in the report.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/imagesource 5.png?mtime=1360935787" alt="" width="208" height="167" />
</p>

To add the image to the report, I drag an image control into the header. I select the image source as Embedded and use the drop-down arrow to select logo_prod as my image.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/imagesource 6.png?mtime=1360935787" alt="" width="574" height="517" />
</p>

When I run the report, the image is in the header.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/imagesource 7.png?mtime=1360935787" alt="" width="448" height="269" />
</p>

I want to use the image as it is stored in the database. I drag an image control into the header. I select the image source of Database. I choose the field in the dataset my image is stored in, and specify the image MIME type.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/imagesource 8.png?mtime=1360935787" alt="" width="576" height="509" />
</p>

When I run the report, the image is in the header.

<p style="text-align: center;">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/grrlgeek/imagesource 9.png?mtime=1360935787" alt="" width="471" height="267" />
</p>

**Further Reading:** 

[Images (Report Builder and SSRS)][2]

 [1]: /index.php/DataMgmt/ssrs/making-data-tell-a-story
 [2]: http://msdn.microsoft.com/en-us/library/dd239394.aspx