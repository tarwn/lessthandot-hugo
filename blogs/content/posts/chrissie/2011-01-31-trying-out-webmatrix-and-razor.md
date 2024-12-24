---
title: Trying out Webmatrix and Razor with VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-31T11:20:00+00:00
ID: 1023
excerpt: |
  Introduction
  
  Last week I attended a webcamp with Scott Hanselman and he showed Webmatrix and how cool it really is. So today I had some time to try it out and make myself a cool website.
  
  Installation
  
  He said it was only 8MB to download. It is o&hellip;
url: /index.php/webdev/uidevelopment/ajax/trying-out-webmatrix-and-razor/
views:
  - 4604
categories:
  - AJAX
tags:
  - razor
  - vb.net
  - webmatrix

---
## Introduction

Last week I attended a webcamp with Scott Hanselman and he showed Webmatrix and how cool it really is. So today I had some time to try it out and make myself a cool website.

## Installation

He said it was only 8MB to download. It is only 8MB to download untill you click the install button after which it starts downloading a whole lot more software. It probably is no problem for people with fast internet. 

YOu can download webmatrix from the [ASP.Net webpages][1]. YOu just have to click the big green button on the [install page][2].

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix1.png?mtime=1296477455"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix1.png?mtime=1296477455" width="904" height="596" /></a>
</div>

That will download the webinstaller which is only 8MB.

And then the downloading begins.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix2.png?mtime=1296477467"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix2.png?mtime=1296477467" width="700" height="480" /></a>
</div>

It will install IIS-express and some other things it needs.
  
This is a list of things it installed on my machine.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix3.png?mtime=1296477788"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix3.png?mtime=1296477788" width="700" height="480" /></a>
</div>

## First site

So the installation was pretty painless. Now let&#8217;s get me a site. I used a template and used starter site (seemed the most logical thing a novice would click).

This is were you end up after you click ok.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix4.png?mtime=1296478312"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix4.png?mtime=1296478312" width="1260" height="745" /></a>
</div>

And this is what happens when you click run.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix5.png?mtime=1296478326"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix5.png?mtime=1296478326" width="947" height="362" /></a>
</div>

That was simple and quick. Now let&#8217;s see if I can do some razor things with it.

## Razor

Just doing some quick and dirty things with it now. AS usual you have to jump through hoops to use VB as your language. YOu have to pick a vbhtml for this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix6.png?mtime=1296478675"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix6.png?mtime=1296478675" width="1244" height="707" /></a>
</div>

It is not in common nor in suggested, you have to go to all.

So I create a Page called Page and added it to the menu. You have to change _SiteLayout.cshtml for that. Like this.

```html
&lt;ul id="menu"&gt;
                    &lt;li&gt;&lt;a href="@Href("~/")"&gt;Home&lt;/a&gt;&lt;/li&gt;
                    &lt;li&gt;&lt;a href="@Href("~/Page")"&gt;Page&lt;/a&gt;&lt;/li&gt;
                    &lt;li&gt;&lt;a href="@Href("~/About")"&gt;About&lt;/a&gt;&lt;/li&gt;
                &lt;/ul&gt;```
When you make a new page as vbhtml you get all this.

```html
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
    &lt;head&gt;
        &lt;meta charset="utf-8" /&gt;
        &lt;title&gt;&lt;/title&gt;
    &lt;/head&gt;
    &lt;body&gt;
        
    &lt;/body&gt;
&lt;/html&gt;```
Which we don&#8217;t need. I want my page to look like the rest.

Finding the right syntax is a bit tricky if you insist on doing VB.Net since most examples are in C#.

But here it is.

```html
@Code 
    Layout = "~/_SiteLayout.cshtml"
    Page.Title = "Welcome to my Web Site!"
End Code
&lt;p&gt;
    ASP.NET Web Pages make it easy to build powerful .NET based applications for the web.
   
&lt;/p&gt;```
That now looks the same as the default page.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix7.png?mtime=1296479431"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix7.png?mtime=1296479431" width="938" height="345" /></a>
</div>

Next thing I wanted to find out is how to do a for loop.

This is what it looks like.

```html
@Code 
    Layout = "~/_SiteLayout.cshtml"
    Page.Title = "Welcome to my Web Site!"
End Code
&lt;p&gt;
   ASP.NET Web Pages make it easy to build powerful .NET based applications for the web.
   In VB.Net.
    &lt;ul&gt;
    @for i as integer = 1 to 10
        @&lt;li&gt;@i&lt;/li&gt;
    next
    &lt;/ul&gt;    
&lt;/p&gt;
```
And this the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix8.png?mtime=1296479749"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/Webmatrix8.png?mtime=1296479749" width="198" height="285" /></a>
</div>

I was a bit surprised with the syntax, I don&#8217;t think it is all that consistent (why no @ before the next? and why do I need the @ in front of the li?) but I could get used to it over time.

## Conclusion

It was fun playing with it. It is very fast to install and a breeze to get it working (I can still remember the nightmares I used to have with eclipse and such). The easy of getting started is crucial to get people in to development. You don&#8217;t want to scare people away just because they can&#8217;t get it installed. But don&#8217;t worry, people who are not willing to learn the syntax or learn anything about development will soon give up.

 [1]: http://www.asp.net/webmatrix
 [2]: http://www.microsoft.com/web/gallery/install.aspx?appid=webmatrix