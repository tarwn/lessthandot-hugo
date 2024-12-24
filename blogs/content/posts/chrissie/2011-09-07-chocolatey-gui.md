---
title: Chocolatey GUI
author: Christiaan Baes (chrissie1)
type: post
date: 2011-09-07T17:12:00+00:00
ID: 1308
excerpt: 'I blogged about Chocolatey before and I even made it work behind my proxy, this change  is even in the latest version so go get it. And now I made a GUI for it.'
url: /index.php/desktopdev/mstech/chocolatey-gui/
views:
  - 10259
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'
  - chocolatey
  - nuget

---
## Introduction

I [blogged about Chocolatey before][1] and I even [made it work behind my proxy][2], this change is even in the latest version so go get it. And now I made a GUI for it.

And it works on the newly created website too. Namely <http://chocolatey.org> made by the always amazing Rob Reynolds aka ferventcoder.

> To install chocolatey now, open a powershell prompt, paste the following and type Enter: iex ((new-object net.webclient).DownloadString(&#8220;http://bit.ly/psChocInstall&#8221;))

## The GUI

The GUI just executes the powershell commands in the background. Something [I also blogged about before][3].

This GUI is my very first open source project and the code is up on [github][4]. Woohoo.

I wrote it in C# and not VB.Net. But I might make a VB-version very soon. Just for the hell of it.

It is a pre-alpha but most things work.

You can see the installed packages.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey7.png?mtime=1315421686"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey7.png?mtime=1315421686" width="619" height="471" /></a>
</div>

If it detects that an update is available than you can just click the button and it will update.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey8.png?mtime=1315421996"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey8.png?mtime=1315421996" width="619" height="471" /></a>
</div>

If you click Available packages than you get all the packages on chocolatey.org.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey10.png?mtime=1315422308"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey10.png?mtime=1315422308" width="619" height="471" /></a>
</div>

And if you choose a package that you don&#8217;t already have than you can click the install button and it will install.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey9.png?mtime=1315422297"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey9.png?mtime=1315422297" width="619" height="471" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey10.png?mtime=1315422308"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/Chocolatey10.png?mtime=1315422308" width="619" height="471" /></a>
</div>

## Conclusion

It was fun making this and it still needs some work. Thanks again to Rob for this great tool.

 [1]: /index.php/DesktopDev/MSTech/chocolatey-apt-get-for-windows
 [2]: /index.php/DesktopDev/MSTech/getting-chocolatey-to-work-when
 [3]: /index.php/DesktopDev/MSTech/powershell-in-your-vb-net-1
 [4]: https://github.com/chrissie1/chocolatey-Explorer