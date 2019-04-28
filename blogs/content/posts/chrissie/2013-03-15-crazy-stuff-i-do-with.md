---
title: Crazy stuff I do with Nancy
author: Christiaan Baes (chrissie1)
type: post
date: 2013-03-15T12:58:00+00:00
ID: 2035
excerpt: Crazy stuff I do with Nancy.
url: /index.php/webdev/serverprogramming/crazy-stuff-i-do-with/
views:
  - 2172
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - vb.net

---
No, not that.

I had the need to write documentation for my services. I thought having them as html would be nice and good enough. 

So I made a bunch of vbhtml razorviews because I want my pages to be pretty and use the masterpage I already made for my main site. You know, to be consistent. 

So I had this.

  * page1.vbhtml
  * page2.vbhtml
  * page3.vbhtml
  * page4.vbhtml
  * index.vbhtml

And to show those pages I just have this in my module.

```
MyBase.Get("/{Title}") = Function(parameters)
  Return Negotiate.WithView("documentation/" & parameters.Title)
End If```
So calling page1 is as easy as 

<code class="codespan">/documentation/page1</code>

But what if someone did

<code class="codespan">/documentation/page12</code>

They would get an error.

But, meh, I don&#8217;t want that. I want them to return to the index instead. 

So here is my solution.

```
Public Sub New(viewlocator As IViewLocator)
            MyBase.New("/documentation")
            MyBase.Get("/{Title}") = Function(parameters)
                                         If viewlocator.LocateView("documentation/" & parameters.Title, Context) IsNot Nothing Then
                                             Return Negotiate.WithView("documentation/" & parameters.Title)
                                         Else
                                             Return Negotiate.WithView("documentation/index")
                                         End If
                                     End Function```
With help from GrumpyDev.

Quick and easy. 

I do not want to put to much work in the documentation since noone will ever read it anyway.