---
title: Picking a new IoC container/Replacing structuremap
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-20T06:08:00+00:00
ID: 1391
excerpt: "Anyway I have been using structuremap with great success since the beginning. But frameworks are like handbags. Once you have it long enough you just want a new one. Not only to brag about it with your girlfriends but sometimes just have something new and shiny that fits better with that new pair of shoes you bought. Now I won't change IoC containers as fast as I change handbags so I have to make an informed decision."
url: /index.php/desktopdev/mstech/picking-a-new-ioc-container/
views:
  - 6490
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - ioc contianer
  - vb.net

---
I have been using Structuremap for years now. Which is obvious since I have [28 blogpost mentioning it][1]. The first of those posts going back all the way to 2008 (Considering I only started blogging in 2008.

Anyway I have been using structuremap with great success since the beginning. But frameworks are like handbags. Once you have it long enough you just want a new one. Not only to brag about it with your girlfriends but sometimes just have something new and shiny that fits better with that new pair of shoes you bought. Now I won&#8217;t change IoC containers as fast as I change handbags so I have to make an informed decision.

So first of all, what is it I want from an IoC?

  * Must be compatible with VB.Net (duh).
  * I want a fluent interface.
  * I don&#8217;t like attributes.
  * Good documentation.
  * Property injection
  * Constructor injection
  * Active community

First thing I found when looking on Google for IoC containers .Net was [a post by mister Hanselman][2]. The post is from 2008 and thus kind of old and might be obsolete. But it gave me a list of things to check out. 

So here is the list of containers that made the shortlist.

  * [Structuremap][3] obviously but I already know this.
  * [Autofac][4]
  * [Ninject][5]

And the winner is&#8230; Autofac.

Yes it has amazing documentation and I found what I wanted in there. And it does all that I had on my list and more.

So why did I discard all the others&#8230; Well you know how it is when you go in to the handbag shop and you just fall in love with the first one you see? Yeah that&#8217;s about it. 

In all honesty, when picking a framework you can&#8217;t make a decision on whether you are going to like it or not just by looking at the doc. You can&#8217;t even tell whether it is any good after doing a little demo app. You can only tell if it is any good and is going to give you pain by using it on a real live program. And no matter what that is going to hurt a little. Because sometimes it&#8217;s the little things you would never have thought about that make a difference.

 [1]: /index.php/All/?s=structuremap&advm=&advy=&author=7
 [2]: http://www.hanselman.com/blog/ListOfNETDependencyInjectionContainersIOC.aspx
 [3]: http://structuremap.net/structuremap/index.html
 [4]: http://code.google.com/p/autofac/
 [5]: http://ninject.org/