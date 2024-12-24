---
title: Microsoft Report Viewer 2012 Runtime redistributable package for Visual studio 2012 RTM
author: Christiaan Baes (chrissie1)
type: post
date: 2012-09-04T11:27:00+00:00
ID: 1710
excerpt: There is a new reportviewer for Visual studio 2012. Here is how I found it.
url: /index.php/desktopdev/mstech/microsoft-report-viewer-2012-runtime/
views:
  - 26255
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 2012
  - reportviewer

---
When you install Visual studio 2012 RTM you get, amongst some other stupid surprises, a new version of the Microsoft reportviewer control. Hooray. 

There are worst things in life, except that all my clients have reportviewer 2010 installed. But that was quickly fixed, just download the new redistributable. 

Except the lastest you can find out there is the beta version.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/reportviewer/reportviewer2012_2.png?mtime=1346762396"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/reportviewer/reportviewer2012_2.png?mtime=1346762396" width="944" height="660" /></a>
</div>

And how do I know this is the same version I have on my dev machine??

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/reportviewer/reportviewer2012_3.png?mtime=1346762501"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/reportviewer/reportviewer2012_3.png?mtime=1346762501" width="374" height="166" /></a>
</div>

[<img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/reportviewer/reportviewer2012_1.png?mtime=1346762383" width="931" height="528" />][1]

As you might see those are not exactly the same version but who cares it&#8217;s such a tiny difference the RTM ends in 16 and the beta ends in 11. But I can tell you it works. 

Except that this time you also have to install the sqlclrtypes perquisite. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/reportviewer/reportviewer2012_4.png?mtime=1346765087"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/reportviewer/reportviewer2012_4.png?mtime=1346765087" width="495" height="250" /></a>
</div>

<span class="MT_red">Please someone at Microsoft instead of making new icons and themes and Uppercase menus, get your act together and check these things before you start sending things out. It&#8217;s not that hard, it&#8217;s called a checklist.</span>

<span class="MT_red">Oh, and there is an x86 and x64 version of the SQLSysClrTypes installer and they just happen to share the same name, joy oh joy.</span>

 [1]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/reportviewer/reportviewer2012_1.png?mtime=1346762383