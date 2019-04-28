---
title: Listen to yourself
author: Christiaan Baes (chrissie1)
type: post
date: 2008-02-29T01:05:12+00:00
ID: 23
url: /index.php/desktopdev/mstech/vbnet/listen-to-yourself/
views:
  - 3048
categories:
  - VB.NET

---
Well :oops:, I can still hear myself say to everybody who wanted to hear it &#8220;Keep the objects that you are returning in your webservices simple&#8221;. So this means, don&#8217;t return complex objects like a dataset, datatable, arraylist or all other kinds of collections. Make a POCO (Plain old C# object) and pass that to the client.

So yesterday I switched to VS 2008 and the first thing I do is compile the thing and get the application out there. And guess what, one of my webservices started returning gibberish. So after a while I noticed that this webservice (not written by me) returned an Arraylist and although I didn&#8217;t write the webservice myself I should have checked before deploying. I changed the ArrayList to a simple array of objects and hey presto, everything worked again. Happy me.

So next time listen to me and I will do the same:D.