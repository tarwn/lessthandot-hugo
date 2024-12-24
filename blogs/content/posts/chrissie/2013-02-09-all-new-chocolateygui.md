---
title: All new ChocolateyGUI
author: Christiaan Baes (chrissie1)
type: post
date: 2013-02-09T14:55:00+00:00
ID: 1981
excerpt: An all new version of ChocolateyGUI is out.
url: /index.php/sysadmins/os/windows/all-new-chocolateygui/
views:
  - 67179
categories:
  - Windows
  - Windows 7
  - Windows 8
tags:
  - chocolatey
  - chocolateygui
  - powershell

---
## Introduction

I admit I had been neglecting ChocolateyGUI for the last year or so. And it was no longer compatible with the current version of Chocolatey.

But a few weeks ago a gentle soul called [Miracula][1] decided it was time to help and I made her a contributor the moment she did. After all it was my very first pull request ever and I was happy. 

This weekend I decided to give ChocolateyGUI some more of my love and attention. And noticed that Miracula had been very busy indeed. 

So I decided that it was time for a [new package][2]. 

And here it is.

## Installation

Installation is very simple.

```
cinst ChocolateyGUI```
That&#8217;s it. As long as you have Chocolatey installed of course. But even that is [easy to install][3].

## New features

Just look how pretty it is.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/ChocoGUI1.png?mtime=1360428609"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/chocolatey/ChocoGUI1.png?mtime=1360428609" width="1037" height="534" /></a>
</div>

You can search in the list. It is a StartsWith list.
  
You can install the available package by clicking on the checkbox.
  
You can uninstall a package.
  
You can update chocolatey, because it will be in the list of installed packages.
  
It is a lot faster.
  
It shows a lot more information about the selected package.

## Open source

And the code is still [open source][4]. And we accept pull-requests and feature requests.

## Conclusion

I want to thank Miracula again for the awesome work she did.

<span class="MT_red">Update</span>

ChocolateyGUI has a dependecy on powershell 3.0. You can install that by doing cinst powershell. In the next version we wil install this as a dependency package.

 [1]: https://twitter.com/miracula_de
 [2]: http://chocolatey.org/packages/ChocolateyGUI
 [3]: http://chocolatey.org/
 [4]: https://github.com/chrissie1/chocolatey-Explorer