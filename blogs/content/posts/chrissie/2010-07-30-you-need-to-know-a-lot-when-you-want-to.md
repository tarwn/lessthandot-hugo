---
title: You need to know a lot when you want to be a developer.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-07-30T08:06:35+00:00
ID: 857
excerpt: |
  I was looking at the stack they used for the new channel9 website.
  
  MVC 2.0, Unity (from P&amp;P), NHibernate, Fluent NHibernate, Memcached, Enyim Managed Memcached driver, Azure – Fabric, Storage, Diagnostics, SQL Azure, xUnit (testing only), Live ID&hellip;
url: /index.php/architect/hardwareinfrastructuredesign/you-need-to-know-a-lot-when-you-want-to/
views:
  - 4010
categories:
  - Hardware and Infrastructure Design

---
I was looking at the stack they used for the new channel9 website.

> MVC 2.0, Unity (from P&P), NHibernate, Fluent NHibernate, Memcached, Enyim Managed Memcached driver, Azure – Fabric, Storage, Diagnostics, SQL Azure, xUnit (testing only), Live ID, Spark View Engine, Akismet (spam filtering service), AntiXSS, Tinymce, jQuery, and Silverlight.

You can watch the video [by Mike Sampson on how they built the new site][1] if you want to know more.

But have you counted the number of frameworks/technologies required for such a site? I was going to say simple site but I don&#8217;t think that exists anymore. I count 16 (I didn&#8217;t count Storage and diagnostics). And I&#8217;m sure they forgot a whole bunch that you just take for granted.

Then I started counting the stack I use at work. In no particular order.

  * nHibernate
  * WPF
  * Winforms
  * Log4net
  * NUnit
  * Rhino mocks
  * SQL-server
  * StructureMap
  * Microsoft reporting
  * ITextsharp

Seems a whole lot less than what you need for web development. But the problem remains the same. You have to know all those things well to make them work together well and you need to know the tools that you can use to make them work together well.

Here are just a few of the tools I use to make all that work well. Again in no particular order.

  * SSMS
  * Redgate toolbelt
  * NDepend
  * Visual Studio 2010
  * Ncover/DotCover
  * Resharper
  * Finalbuilder
  * An enormous amount of third party controls
  * NHProf
  * SQLCop 😉
  * VisualSVN
  * VMWare
  * Windows
  * Oracle
  * dotTrace
  * Sharepoint

All these tools and frameworks you have to know and know well. Some you will only use once in a while but you have to know they exist and you have to know how to use them. And lets not forget all those things you tried and rejected. I think those make an even longer list.

I&#8217;m sure you could spend days and weeks just getting to grips with NDepend but you just don&#8217;t have the time. And by the time you know all the above it is surely outdated. 

And then you also have to know something about hardware. 

But does that mean that the life of a developer is hard and harder than most other professionals? No, it isn&#8217;t. I do the developing thing part time and the other part of the job takes just as much effort and commitment to do right as the development side of things. It&#8217;s all part of the job. But you could do what you do with a lot less effort and commitment and some people do. But that doesn&#8217;t make you better or worse then anyone else just different. Different people have different priorities. 

But I can sure understand why people don&#8217;t want to learn new things and just do things with what they have now. 

I&#8217;m just in it because I like to do what I do. As soon as it stops being fun I will move on. But for the moment I still have lots to learn and I&#8217;m not as young as I used to be 😉 so it takes a little longer.

 [1]: http://channel9.msdn.com/shows/Going+Deep/Mike-Sampson-Inside-Rev9/