---
title: Nancy, IIS 7 and the PUT command
author: Christiaan Baes (chrissie1)
type: post
date: 2013-03-13T06:49:00+00:00
ID: 2031
excerpt: When using Nancy and IIS you might get a 405 when trying to use it from your application. Here is how I fixed it.
url: /index.php/webdev/serverprogramming/nancy-iis-7-and-the/
views:
  - 3693
categories:
  - ASP.NET
  - Server Programming
tags:
  - iis
  - nancy

---
When you use PUT in your Nancy application and you host your application on an IIS 7 server then you might get a 405 error (Method not allowed).

The solution to this problem is very simple but I will put it here for me.

The problem is that you might have the webdavmodule installed on your IIS and that is gobbling up all the PUT requests. 

So the simple solution is to remove the webdavmodule.

You can do this via the IIS manager and just remove it from your site, but if you use webdeploy it will add it back in the next time you deploy. 

So it is better to use the webconfig to tell IIS you really don&#8217;t want it for your particular site.

So you add this to the web.config

```xml
&lt;system.webServer&gt;
    &lt;modules&gt;
       &lt;remove name="WebDAVModule"/&gt;
    &lt;/modules&gt;
&lt;/system.webServer&gt;```
Simple, and it works. Let&#8217;s move on.

No TDD was involved in the making of this blogpost.