---
title: The black wallpaper problem in windows 7 and a group policy
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-28T12:39:00+00:00
ID: 1016
excerpt: |
  Introduction
  
  The computers in my domain get a standard (but very well designed) background to look at. I set this via a group policy.
url: /index.php/sysadmins/os/windows/2003server/the-black-wallpaper-problem-in/
views:
  - 10446
categories:
  - 2003 Server
tags:
  - bug
  - group policy
  - wallpaper
  - windows 2008 r2
  - windows 7

---
## Introduction

The computers in my domain get a standard (but very well designed) background to look at. I set this via a group policy.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Wallpaper.png?mtime=1296224855"><img alt="" src="/wp-content/uploads/users/chrissie1/Wallpaper.png?mtime=1296224855" width="723" height="302" /></a>
</div>

## The problem

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Wallpaper2.png?mtime=1296224957"><img alt="" src="/wp-content/uploads/users/chrissie1/Wallpaper2.png?mtime=1296224957" width="723" height="302" /></a>
</div>

Yep a black background instead of my beautifully designed background. 

You can find several solutions on google but there is one that works.

And it&#8217;s this one [KB977944][1]. I would link to the download page but MS has decided that this hotfix is not for the faint of hart and you have to request it and then you get an email and the you may unzip it after you give it a password. That password is limited in time also. And you get all kinds of nasty warnings that you should be carefull and backup things and such. Since this was not my computer I just ignored all the warnings and installed the hotfix and it worked.

Apparently this not only happens on windows 7 but also on Windows 2008 R2.

## Conclusion

I hate companies like Microsoft and Jetbrains that think resolving bugs is optional or even vote-based. Solving bugs should be mandatory, that&#8217;s what I do anyway.

 [1]: http://support.microsoft.com/kb/977944