---
title: Microsoft reportviewer and setting displaymode to printlayout
author: Christiaan Baes (chrissie1)
type: post
date: 2009-09-15T04:10:30+00:00
ID: 560
excerpt: |
  When working with the reportviewer I found it very annoying that it shows its report in a not printlayout mode. Like this
  
   
  
  But I want it in printlayout mode by default.
  
   
  
  After looking through Google and the properties and nearly giving up&hellip;
url: /index.php/desktopdev/mstech/microsoft-reportviewer-and-setting-displ/
views:
  - 13775
categories:
  - Microsoft Technologies
tags:
  - microsoft reporting engine
  - reportviewer
  - vb.net

---
When working with the reportviewer I found it very annoying that it shows its report in a not printlayout mode. Like this

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/reportviewer/reportviewer1.jpg" alt="" title="" width="423" height="475" />
</div>

But I want it in printlayout mode by default.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/reportviewer/reportviewer2.jpg" alt="" title="" width="406" height="395" />
</div>

After looking through Google and the properties and nearly giving up I found that they made this a function.

```vbnet
_viewer.SetDisplayMode(DisplayMode.PrintLayout)```
Nope no DisplayMode property in sight. While all the other settings are properties. Oh joy.

SO when you intend to make an API, please be consistent.