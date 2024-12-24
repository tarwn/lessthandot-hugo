---
title: 'Resharper 6 EAP and VB.Net: First impressions'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-31T08:53:00+00:00
ID: 1194
excerpt: |
  Introduction
  
  I installed Resharper 6 EAP (The nightly builds) just because Hadi Hariri (him again) asked me to. You can read on some of the new features on the Jetbrains blog. And here are some of the things for VB.Net that caught my eye and that are&hellip;
url: /index.php/desktopdev/mstech/resharper-6-eap-and-vb/
views:
  - 2591
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - resharper
  - vb.net

---
## Introduction

I installed Resharper 6 EAP (The nightly builds) just because Hadi Hariri (him again) asked me to. You can read on some of the new features on the [Jetbrains blog][1]. And here are some of the things for VB.Net that caught my eye and that are very handy indeed. I already blogged why you would want [Resharper even for VB.Net][2].

## Solution wide code inspection

In VB.Net we already have pretty good error listing at codetime but Resharper 6 add a lot to that. it found 30 errors in my solution that the compiler did not pickup on. A few of those however were not errors but this is still an EAP. The first time you turn on solution wide code inspection it takes a while to go through all the files, but I think it was reasonable.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_1.png?mtime=1306838583"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_1.png?mtime=1306838583" width="230" height="122" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_2.png?mtime=1306838598"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_2.png?mtime=1306838598" width="702" height="464" /></a>
</div>

And this is one of the things it caught.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_3.png?mtime=1306838775"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/resharper6_3.png?mtime=1306838775" width="1461" height="105" /></a>
</div>

## The testrunner

The testrunner now also detects parameterized unittests and splits them out in seperate tests.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/paramtests.png?mtime=1306838934"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/paramtests.png?mtime=1306838934" width="1024" height="423" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/paramtests2.png?mtime=1306838946"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/paramtests2.png?mtime=1306838946" width="370" height="125" /></a>
</div>

but as you can see it does not yet do this on analysis of the project, just when you run the tests. But I hear that is going to be solved very soon.

## Conclusion

The first impression was very good. Now I will just have to work with it for a while to see if it is faster than the previous version.

 [1]: http://blogs.jetbrains.com/dotnet/tag/resharper-6/
 [2]: /index.php/DesktopDev/MSTech/resharper-is-usefull-even-for