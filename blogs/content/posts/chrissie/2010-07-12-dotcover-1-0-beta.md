---
title: dotCover 1.0 Beta
author: Christiaan Baes (chrissie1)
type: post
date: 2010-07-12T09:47:53+00:00
ID: 841
url: /index.php/desktopdev/mstech/dotcover-1-0-beta/
views:
  - 2204
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - dotcover
  - vb.net

---
## Introduction

A couple of days ago or perhaps a few weeks ago, [dotCover 1.0 Beta][1] was released. dotCover is a code coverage tool. 

Or like [Jetbrains][2] say.

> JetBrains dotCover is a new code coverage tool for .NET developers.

And here is its feature list.

> dotCover features include:
> 
> * Reporting statement-level coverage in .NET applications.
      
> * Highlighting for uncovered code in Visual Studio.
      
> * Detecting which tests cover a particular location in code.
      
> * Integration with Visual Studio 2005, 2008 and 2010.
      
> * Integration with [ReSharper][3] to show test coverage. 

Now why would I be excited about a new tool? I already have [nCover][4] and it does a great job. So why, you ask? Because it is integrated with the unittestrunner that comes with [ReSharper][3] and I use that all the time, that&#8217;s why. nCover isn&#8217;t integrated in Visual Studio so you need another program open at the time of development.

## Why and why not

Do you really need a tool like this? Not really, because if you write tests first then you should always have 100% coverage. But we are all human and we all make mistakes, mainly caused by others of course. So we need it to check if we did a good job. We need it to check if we covered all the bases. But a code coverage tool will never tell you if your tests are actually of good quality, it will just tell you that your tests have gone over all the code. It will not tell you if you have tested all the edge cases. And it won&#8217;t tell you if your design is any good. It will just tell you if you missed a spot, just like your mother-in-law that watches you while you clean her car or even your own car.
  
So 100% code coverage tells you nothing but 99% code coverage says you forgot something. Perhaps that something you forgot was intentional so you will also need a way to filter out the intentionally not covered code.

## Using it

First I installed the new version of Resharper, namely [version 5.1][5] and then the [dotCover 1.0 Beta][1].

First of all we run our test without coverage.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/dotCover/dotcover1.png" alt="" title="" width="452" height="732" />
</div>

As you can see, we now have 2 tabs in the bottom of our Unit test sessions window, namely Output and Coverage. Output is empty because all the tests passed and Coverage is empty because we need to tell it to do coverage. But as you can see at the top we now also have an extra button, namely the button with the dotCover logo on it. If we click this then the Coverage tab will be filled.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/dotCover/dotcover2.png" alt="" title="" width="725" height="732" />
</div>

As an example, I ran the tests for one class (not a very good idea but it was just to show test things and get a nice screenshot). As you can see, it will show the covered code in green and the not covered code in pink.
  
It will also show this in your code. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/dotCover/dotcover3.png" alt="" title="" width="539" height="324" />
</div>

You can thus easily see which lines you failed to test.

## Conclusion

I like the integration with ReSharper, but this is still a beta and needs some work, in my case the GUI did not refresh when I changed the size of the window, and a few other minor things. But it did not really slow down things all that much either so that is a good thing. I am looking forward to the release of this and I will be using it from now on.

 [1]: http://www.jetbrains.com/dotcover/
 [2]: http://www.jetbrains.com/
 [3]: http://www.jetbrains.com/resharper/
 [4]: http://www.ncover.com/
 [5]: http://www.jetbrains.com/resharper/download/