---
title: Winforms richtextbox and a memoryleak
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-13T04:59:00+00:00
ID: 1173
excerpt: |
  Introduction
  
  I use a few richtextboxes to show some information on a screen. That information gets refreshed every 10 seconds. This application runs 24/7 and I don't want to spend more time on it than the bare minimum. It's cool to have but not busin&hellip;
url: /index.php/desktopdev/mstech/winforms-richtextbox-and-a-memoryleak/
views:
  - 2806
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - richtextbox
  - windows forms

---
## Introduction

I use a few richtextboxes to show some information on a screen. That information gets refreshed every 10 seconds. This application runs 24/7 and I don&#8217;t want to spend more time on it than the bare minimum. It&#8217;s cool to have but not business critical. The problem was that it had a small memoryleak. 

## The problem

The problem, it turns out, is that the richtextbox retains an unlimited amount of undo information. So everytime you update the richtextbox it adds to this undo information. The first reactions could be to use a new richtextbox every time you use need to put new text in it. However that also has some problems because it does not dispose all that well. I already blogged about that problem a while back &#8220;[Not all memoryleaks are the same and USER objects.][1]&#8221;

Apparently the Clear method of the Richtextbox does not clear the undo information either. You need the very disciptive ClearUndo for that. 

## Conclusion

If you have a richtextbox on your winforms application don&#8217;t forget to clear the undo information from time to time. 

If you make an undo list of commands then limit the number of elements you want to store in it so as not to upset the user of your API.

 [1]: /index.php/DesktopDev/MSTech/not-all-memoryleaks-are-the-same-and-use