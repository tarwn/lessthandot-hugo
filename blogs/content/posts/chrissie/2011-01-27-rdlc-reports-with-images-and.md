---
title: Rdlc reports with images and setting the backgroundcolor of a textbox
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-27T13:37:00+00:00
ID: 1014
excerpt: |
  Introduction
  
  For this report I had to make I needed to show an image I had on file, an image that should be made at runtime, actually it was a graph and I needed to show a color or two.
  
  This is what it looked like in the end.
url: /index.php/desktopdev/mstech/msaccess/accessformsreports/rdlc-reports-with-images-and/
views:
  - 6031
categories:
  - Access Forms and Reports
tags:
  - rdlc
  - reports
  - vb.net

---
## Introduction

For this report I had to make I needed to show an image I had on file, an image that should be made at runtime, actually it was a graph and I needed to show a color or two. I databind to objects not SQL.

This is what it looked like in the end.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/rdlc/reportrdlc1.png?mtime=1296141524"><img alt="" src="/wp-content/uploads/users/chrissie1/rdlc/reportrdlc1.png?mtime=1296141524" width="412" height="87" /></a>
</div>

## Solution 1

This is the image that comes from a file. This pretty easy. You just need a property of type string with a path in it. And then set the properties of an image control on the report. First you need to set the property Source to external and then you need to set the property value to something like this.

```
="File://" & First(Fields!Image.Value)```
## Solution 2

The second image was a bit more difficult. You can&#8217;t set a property to type image and then expect it to magically appear on the report. No rdlc reports are object oriented in name only, not in practice. You need your property to be of type Byte() for this to work. That is an array of Bytes. It is however pretty simple to convert your image to an array of bytes. Via a Memorystream.

```vbnet
Dim ms New MemoryStream()
img.Save(ms, System.Drawing.Imaging.ImageFormat.Png)
Dim bytes = ms.ToArray()
```
You can now add an image control to your report and set the source to database and the value to something like this.

```
=Fields!Image.Value```
## Solution 3

Again you can not just set a property of type color. You need one of type string and then you convert your color to a hex representation of that color.
  
Like so

```
_Color = "#" & Hex(Color.R) & Hex(Color.G) & Hex(Color.B)```
You then pick a Textboxcontrol for your report and set its Background color to something like this.

```
=Fields!Color.Value```
And then you get the solution above.

## Conclusion

Solution 2 and 3 where not very intuitive if you work with an object as your datasource. But the I got it to work in the end.