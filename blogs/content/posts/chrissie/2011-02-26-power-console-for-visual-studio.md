---
title: Power console for Visual Studio 2010
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-26T07:18:00+00:00
ID: 1056
excerpt: 'In my attempts to automate the world I learned myself some powershell, and got myself in trouble along the way. But I got to know some very friendly and helpful people along the way, I got a lot of help from Ravikanth Chaganti (blog|twitter). And he got&hellip;'
url: /index.php/desktopdev/generalpurposelanguages/power-console-for-visual-studio/
views:
  - 2689
categories:
  - General Purpose Languages
tags:
  - powerconsole
  - visual studio 2010

---
In my attempts to automate the world [I learned myself some powershell][1], [and got myself in trouble along the way][2]. But I got to know some very friendly and helpful people along the way, I got a lot of help from Ravikanth Chaganti ([blog][3]|[twitter][4]). And he got me thinking with this comment on my blog. 

> you can use the PowerShell console extention to access the $dte object and see what project was open and reopen it later. You can actually script it inside the Visual Studio environment. Check PowerGUIVSX, etc 

So I did not listen and installed Powerconsole instead ;-). Powerconsole is an extension which can easily be installed via the extension manager.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole1.png?mtime=1298710940"><img alt="" src="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole1.png?mtime=1298710940" width="955" height="660" /></a>
</div>

After a Visual studio restart you can then open a powerconsole window by going to the view menu(see picture).

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole2.png?mtime=1298710955"><img alt="" src="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole2.png?mtime=1298710955" width="620" height="500" /></a>
</div>

Now that you have to open you have access to the $dte variable, which has lots of members to play with.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole3.png?mtime=1298710968"><img alt="" src="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole3.png?mtime=1298710968" width="727" height="590" /></a>
</div>

First thing to do was to find out if I could use git from there. But I had a problem there. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole4.png?mtime=1298711240"><img alt="" src="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole4.png?mtime=1298711240" width="168" height="150" /></a>
</div>

The problem is that the workingdirectory of the powerconsole is not the same as your solution.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole5.png?mtime=1298711324"><img alt="" src="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole5.png?mtime=1298711324" width="785" height="156" /></a>
</div>

But we can fix that (if you know how) and I found a [helpful blogpost][5] on the net that had what I wanted.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole6.png?mtime=1298711616"><img alt="" src="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole6.png?mtime=1298711616" width="758" height="201" /></a>
</div>

And now I can continue on my quest to find the holy grail.

I am very happy with my quest so far, since I have learned lots of new things on found out that there are a lot of helpful people in the Powershell world. I actually find the lack of semicolons in Powershell refreshing. And I should have learned this earlier.

 [1]: /index.php/DesktopDev/GeneralPurposeLanguages/my-first-steps-in-powershell
 [2]: /index.php/DesktopDev/GeneralPurposeLanguages/powershell-wmiobject-and-a-corrupt
 [3]: http://www.ravichaganti.com/blog/
 [4]: http://twitter.com/ravikanth
 [5]: http://mark-dot-net.blogspot.com/2010/10/change-to-solution-folder-in-package.html