---
title: Resharper is useful even for VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-25T10:40:00+00:00
ID: 1189
excerpt: |
  Introduction
  
  You will see that many people use Resharper. Most of those are however C# users and you will see most of the demos are in C#. So why should you buy Resharper if you are a VB.Net user (I don't get money nor love from JetBrains for writing&hellip;
url: /index.php/desktopdev/mstech/resharper-is-usefull-even-for/
views:
  - 3291
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - resharper
  - vb.net

---
## Introduction

You will see that many people use [Resharper][1]. Most of those are however C# users and you will see most of the demos are in C#. So why should you buy Resharper if you are a VB.Net user (<span class="MT_red">I don&#8217;t get money nor love from JetBrains for writing this</span>)? After all some of the refactoring methods are just there in VB.Net that are not there in C#. We can do a rename, we can get live errorreporting and we get lots of other things those poor people that use C# don&#8217;t have. So below are some of the reasons why I use it and why I can no longer live without it despite it&#8217;s memoryusage and it sometimes slowing down my machine.

This post was kinda inspired by the neat presentation I saw Hadi Hariri give at techdays. He made it look so simple. My first intention was to ask Hadi Hariri to show us some of the good stuff for VB.Net. But he does not do VB.Net.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper1.png?mtime=1306318958"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper1.png?mtime=1306318958" width="515" height="69" /></a>
</div>

## Resharper

So I will now tell you which parts I use myself and like the most.

### The Alt+Enter thing

I love the Alt+Enter thing, they probably have a name for it but I don&#8217;t care it&#8217;s awesome. I had it in Eclipse and I like it here. Mind you VS seems to have this function to but in a less quick way.

So if you see a type in red you can just do Alt+Enter and it will add an import if it can find a reference.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper2.png?mtime=1306319223"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper2.png?mtime=1306319223" width="428" height="86" /></a>
</div>

If it can&#8217;t find that Class it will propose to create a new one or create a field where appropriate. As you can see it kinda conflicts with the VS thing (the little red thing under the last letter at the end of the name. And when you click on it you get this or apparently you can use the Ctrl+. shortcut to get to it to, thanks to Eli (Tarwn) to point that out to me).

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper3.png?mtime=1306319454"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper3.png?mtime=1306319454" width="400" height="186" /></a>
</div>

I actually have no idea where it comes from or how to turn it off (Yeah I suck).

So perhaps that was not a very good reason to buy Resharper.

### The Alt+Insert thing

When you do Alt+Insert in your class you get this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper4.png?mtime=1306319454"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper4.png?mtime=1306319454" width="400" height="186" /></a>
</div>

Which has some very useful automations in them and Which I use a lot.

### The templates

Both the live templates and the file templates I use a lot. I made my own live templates to make unittest methods fast.

```
''' &lt;summary&gt;
''' $test$
''' &lt;/summary&gt;
&lt;Test&gt;  _ 
Public Sub $test$
   $END$
End Sub```
I just type test and tab and get a testmethod, it&#8217;s kinda like code snippets but a little better. 

File templates are also easy to make. 

### Unittestrunner

And this is what I use most and is the first reason why I use it. The unit test runner is easy and fun to use, but I hear that in VS vNext the MSTest testrunner might be able to do the same.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper6.png?mtime=1306320985"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/Resharper6.png?mtime=1306320985" width="564" height="322" /></a>
</div>

### The refactorings

There are lots of refactorings you can do but I don&#8217;t tend to use them all that often.
  
I tend to use go to declaration or go to implementation a lot more from time to time I might even use find usages which will find your usages a lot better than a normal find will.

## Conclusion

Do I think it is still worth it to buy Resharper for VB10 and VS2010 like I did for VS2008 and VB9? Yes, it is a little less useful since MS added some of that stuff to VB. But I still can not live without my trusty testrunner and I seem to miss Resharper every time I go on a computer without it. But I would like to hear from VB.Neters that also use it and what their usage scenarios are. The best use you get out of Resharper is if you learn all the shortcuts. Your development time will be more fun and you will produce more, but you need to learn one more thing.

 [1]: http://www.jetbrains.com/resharper/