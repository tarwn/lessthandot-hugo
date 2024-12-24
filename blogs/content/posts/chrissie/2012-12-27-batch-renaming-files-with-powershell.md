---
title: Batch renaming files with powershell
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-27T15:13:00+00:00
ID: 1886
excerpt: Renaming files in a batch using powershell.
url: /index.php/desktopdev/mstech/batch-renaming-files-with-powershell/
views:
  - 7281
categories:
  - Microsoft Technologies
tags:
  - batch
  - powershell
  - rename

---
I like powershell and from time to time I even use it.

This time I needed to rename a lot of files in some directory I had.

First thing to note is that these files are on a NAS.

No problem just do

```
cd \192.168.1.152series```
and you&#8217;re on that drive.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powershell/powershellbatchrename2.png?mtime=1356627627"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powershell/powershellbatchrename2.png?mtime=1356627627" width="813" height="252" /></a>
</div>

That doesn&#8217;t work with the normal commandline BTW.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powershell/powershellbatchrename1.png?mtime=1356627545"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powershell/powershellbatchrename1.png?mtime=1356627545" width="677" height="343" /></a>
</div>

So now we are there we can see we have bunch of files.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powershell/powershellbatchrename3.png?mtime=1356627807"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powershell/powershellbatchrename3.png?mtime=1356627807" width="580" height="475" /></a>
</div>

It is clear that these files don&#8217;t say a lot about the contents, I will want to fix that. I want to prefix them all with Stargate. Easy just cd your way to that directory <code class="codespan">Stargate/SG1/Season 1</code> and do this.

```
Dir *.mkv | rename-item -newname { "Stargate " + $_.Name}```
Yep that&#8217;s one line of code.

But darnit I have more than one season of Stargate, I have 10.

Not to worry just go to the Stargate/SG1 directory instead and do this.

```
Get-ChildItem -recurse | Where {$_ -IS [IO.FileInfo]} | rename-item -newname { "Stargate " + $_.Name}```
And done. Oh no, I made a misstake I also have Atlantis and Universe on there so I would better name them Stargate &#8211; SG1.

Not to worry I can just replace what I did before. Like this.

```
Get-ChildItem -recurse | Where {$_ -IS [IO.FileInfo]} | rename-item -newname { -replace "Stargate ","Stargate - SG1 " }```
And I&#8217;m done. Now to just do this for the 2 others and everything is a lot clearer from now on.

Happy powershelling people.