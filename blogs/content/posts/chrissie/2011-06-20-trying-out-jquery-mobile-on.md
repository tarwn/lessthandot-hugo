---
title: Trying out jQuery Mobile on our blog
author: Christiaan Baes (chrissie1)
type: post
date: 2011-06-20T05:18:00+00:00
ID: 1227
excerpt: |
  Introduction
  
  Since I'm on vacation and I have nothing better to do I decided to make a mobile version of our blogs. Not only because it's fashionable but also because old people will have a hard time reading this site on a smartphone and to save band&hellip;
url: /index.php/webdev/serverprogramming/php/trying-out-jquery-mobile-on/
views:
  - 10673
categories:
  - Javascript
  - PHP
  - XHTML and CSS
tags:
  - jquery
  - lessthandot
  - mobile

---
## Introduction

Since I&#8217;m on vacation and I have nothing better to do I decided to make a mobile version of our blogs. Not only because it&#8217;s fashionable but also because old people will have a hard time reading this site on a smartphone and to save bandwidth in bandwidth deprived countries like Belgium (250MB/month costs 10€). So I set about and read some things about [jQuery Mobile][1]. They are up to alpha 4 already so I expect a few betas soon. There are lots of demos and good documentation on their site. Not all OSS have bad documentation.
  
I have not yet put the site up but I will set it up this week for everyone to test.

## The index

First I needed to create an index page which would show the tittle of the latest blogposts. This is what it looks like on an iphone emulator.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/jquery/jquerymobile1.png?mtime=1308552574"><img alt="" src="/wp-content/uploads/users/chrissie1/jquery/jquerymobile1.png?mtime=1308552574" width="413" height="781" /></a>
</div>

First thing to know is that you add the jQuery and jQuery Mobile references and that it uses HTML 5 to do it&#8217;s magic.

```html
&lt;head&gt;
        &lt;link rel="shortcut icon" href="favicon.ico" type="image/vnd.microsoft.icon" /&gt;
	    &lt;link href="http://code.jquery.com/mobile/latest/jquery.mobile.min.css" rel="stylesheet" type="text/css" /&gt;
        &lt;script src="http://code.jquery.com/jquery-1.6.1.min.js"&gt;&lt;/script&gt;
        &lt;script src="http://code.jquery.com/mobile/latest/jquery.mobile.min.js"&gt;&lt;/script&gt;
        &lt;title&gt;Mobile version of Lessthandot&lt;/title&gt;
    &lt;/head&gt;
```
I used the latest unstable release here, I might change that in the future when they do release.

Then I used a page.You simply define it like this.

```html
&lt;div data-role="page" data-theme="b"&gt;&lt;/div&gt;
```
The data-role tag tells it this is a pag and the data-theme let&#8217;s you choose from 5 themes already there but you can make your own if you so desire.

Then we define a header.

```html
&lt;div data-role="header"&gt;
 &lt;h1&gt;Lessthandot&lt;/h1&gt;
&lt;/div&gt;```
Again we use data-role of type header this time.

After that you have the content data-role to add your&#8230;content. And I added a title.

```html
&lt;div data-role="content"&gt;
&lt;h2&gt;Blogs&lt;/h2&gt;
             ```
After that it was time to add the items. I choose a collapsible set.

```
&lt;div data-role="collapsible-set" data-collapsed="true"&gt;```
Which gave me the possibility to add excerpts to it and a more button to open the post. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/jquery/jquerymobile2.png?mtime=1308553200"><img alt="" src="/wp-content/uploads/users/chrissie1/jquery/jquerymobile2.png?mtime=1308553200" width="413" height="781" /></a>
</div>

The list and individual items would look something like this.

```html
&lt;div data-role="collapsible" data-collapsed="true"&gt;    
  &lt;h3&gt;title&lt;/h3&gt;
  &lt;p&gt;excerpt&lt;/p&gt;
  &lt;div data-inline="true"&gt;&lt;a href="index.html" data-role="button"&gt;More&lt;/a&gt;&lt;/div&gt;
&lt;/div&gt;```
I guess by now you can see what it is doing.

I also added a footer for paging on the page.

Which looks something like this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/jquery/jquerymobile3.png?mtime=1308553752"><img alt="" src="/wp-content/uploads/users/chrissie1/jquery/jquerymobile3.png?mtime=1308553752" width="413" height="781" /></a>
</div>

And here is the code.

```html
&lt;div data-role="footer" class="ui-bar"&gt;
  &lt;a href="mobileindex.php?page=1" data-role="button" data-icon="arrow-l"&gt;Previous&lt;/a&gt;
  &lt;a href="mobileindex.php?page=3" data-role="button" data-icon="arrow-r"&gt;Previous&lt;/a&gt;
&lt;/div&gt;```
Look at how you can easily add the icons to it.

Then I made something similar to show the post.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/jquery/jquerymobile4.png?mtime=1308553930"><img alt="" src="/wp-content/uploads/users/chrissie1/jquery/jquerymobile4.png?mtime=1308553930" width="413" height="781" /></a>
</div>

The code for that is pretty much the same. as the above.

## Conclusion

Working with jQuery Mobile is pretty simple and documentation is very good. The only thing to do now is to resize the images so that they fit better on the little screen.

 [1]: http://jquerymobile.com/