---
title: A few reasons why I’m not yet moving to WPF
author: Christiaan Baes (chrissie1)
type: post
date: 2010-09-27T11:05:46+00:00
ID: 909
url: /index.php/desktopdev/mstech/a-few-reasons-why-i-m-not-yet-moving-to/
views:
  - 6412
categories:
  - Microsoft Technologies
tags:
  - wpf

---
## Introduction

I have some very good reasons to switch from Windows forms to WPF. I think from an UX point of view there is so much more you can do in WPF than you can in Windowsforms. The fact that you can easily create styles and create new controls is one of the best reasons to switch IMHO. But it seems that it still has more than a few problems. 

## Problem 1

We all know that the new Visual studio uses WPF. But from time to time I see this.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/WPF/WPFFail1.png" alt="" title="" width="515" height="81" />
</div>

That is not something I want my users to see. This was on a win7 x64 machine with two nvidia 9800GT graphics cards. 

## Problem 2

I have a test environment with VMWare and WinXP VM. And this is what I see when the splashscreen disappears.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/WPF/WPFFail2.png" alt="" title="" width="1006" height="510" />
</div>

The screen is clearly not refreshing like it should. Knowing that most of my clients still run XP worries me.

## Problem 3

And back to the win7 64x with 4 monitors setup. There I see this.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/WPF/WPFFail3.png" alt="" title="" width="525" height="320" />
</div>

The black lines are not supposed to be there and only happen when I move the window from one monitor to another monitor.

## Conclusion

I know WPF uses DirectX and I know these errors are probably going to be blamed on the graphics drivers but I still think this is enough for me to leave it alone and stick with winforms for just a while longer. These are not the kind of problems I want to spend days on to resolve.

Only 3 more posts until the 250th and final post.