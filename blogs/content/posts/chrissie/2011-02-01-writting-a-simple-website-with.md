---
title: Writing a simple website with webmatrix, razor and VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-01T07:51:00+00:00
ID: 1024
excerpt: |
  So how easy is it for a non-webdeveloper to create a simple site with webmatrix and the razor syntax.
  
  Let me start by using the Empty site template this time.
url: /index.php/webdev/serverprogramming/aspnet/writting-a-simple-website-with/
views:
  - 2894
categories:
  - ASP.NET
tags:
  - razor
  - vb.net
  - webmatrix

---
So how easy is it for a non-webdeveloper to create a simple site with webmatrix and the razor syntax.

Let me start by using the Empty site template this time.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/webmatrix2_1.png?mtime=1296504777"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/webmatrix2_1.png?mtime=1296504777" width="1600" height="900" /></a>
</div>

Yep, that looks pretty empty to me.

So we start by adding an vbhtml page and let&#8217;s call it Default.

This is the standard code that you will find in a vbhtml file.

```vbnet
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
    &lt;head&gt;
        &lt;meta charset="utf-8" /&gt;
        &lt;title&gt;&lt;/title&gt;
    &lt;/head&gt;
    &lt;body&gt;
        
    &lt;/body&gt;
&lt;/html&gt;
```
Yes that is HTML 5. 

Now let&#8217;s add a little text and a title to it.

```html
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
    &lt;head&gt;
        &lt;meta charset="utf-8" /&gt;
        &lt;title&gt;My first page.&lt;/title&gt;
    &lt;/head&gt;
    &lt;body&gt;
        &lt;h1&gt;Introduction&lt;/h1&gt;
        &lt;p&gt;First paragraph&lt;/p&gt;
    &lt;/body&gt;
&lt;/html&gt;
```
With this as the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/webmatrix2_2.png?mtime=1296505170"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/webmatrix2_2.png?mtime=1296505170" width="235" height="119" /></a>
</div>

Now I would like to get all the overhead out of that page and make the default.vbhtml a bit less cluttered.

I can use a layout page for that. Just create a new vbhtml page and call it layout.

We just have to add a few bits to the standard html that is created for us. There is one bit that is really needed and that is @RenderBody() because that will render the body. And I also added @Page.Ttile since every page can now set it&#8217;s own title instead of having the same one for all. 

```html
&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
    &lt;head&gt;
        &lt;meta charset="utf-8" /&gt;
        &lt;title&gt;@Page.Title&lt;/title&gt;
    &lt;/head&gt;
    &lt;body&gt;
        @RenderBody()
    &lt;/body&gt;
&lt;/html&gt;```
Now we have to change our Default page to use this layout.

```html
@Code
    Layout = "~/Layout.vbhtml"
    Page.Title = "My first page."
End Code
&lt;h1&gt;Introduction&lt;/h1&gt;
&lt;p&gt;First paragraph&lt;/p&gt;```
As you can see we removed all the redundant code and added a code block. The codeblock tells it which Layout to use and what title it should set. 

This is the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/webmatrix2_2.png?mtime=1296505170"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/webmatrix/webmatrix2_2.png?mtime=1296505170" width="235" height="119" /></a>
</div>

Yep exactly the same as before. As we wanted it. So that was pretty easy. And it was especially very fast. It&#8217;s very nice to be able to use a simple IDE from time to time. But for the bigger work I would still prefer VS.Net with Resharper all though it tends to crash several times a year.