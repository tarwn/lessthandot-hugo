---
title: jquery and a pinch of ajax
author: Christiaan Baes (chrissie1)
type: post
date: 2009-12-30T19:00:51+00:00
ID: 658
url: /index.php/webdev/uidevelopment/ajax/jquery-and-a-pinch-of-ajax/
views:
  - 2222
categories:
  - AJAX
tags:
  - ajax
  - jquery

---
This is a post for beginners because that is what I am.

Today I was adding some Ajax to our site. Seeing that this is my first day of Ajax and that I only have very little experience with jquery, I thought it would be hard. So I started off with little things.

First, I wanted to add a summary to our authorlist page. But I&#8217;ll talk about that in another article.

I wanted to provide users a way to access some of phpBB&#8217;s built-in functionality without the need for a full postback, so that the user is not taken away from the page. I chose something simple and found out how you can do this with a bit of ajax and jquery.

This thing was there in the form of a link and I don&#8217;t want to change any of the html since I still want that the way it is for people who use no javascript. 

It is very easy.

```javascript
$(document).ready(function()
{
	$("#id").click(function()
	{
		var str = $("#id").text();
		$.ajax
		({
        type: "GET",
        url: $("#id")[0].href,
        success: function(msg) 
			{  
				$("#id").text(str == 'old' ? 'new' : 'old');
            }
        })
    	return false;
    });
});```
First thing I found out was that I needed to return false to override the normal behaviour of a link. Without this, the normal postback will just take place and your script is pretty useless. 

Next, I wanted to do the postback in an asynchronous way. For this I can use the jquery ajax method with type GET I just use the link that is there in the href (<code class="codespan">$("#id")[0].href</code>) which is pretty cool since I could do this for an entire class of links. then I just change the text of the link if it is old then it becomes new else it becomes old. 

Pretty simple I think. Although it took me some time to find out that if you have a syntax error somewhere in the jquery script, then it just does nothing. And if you do <code class="codespan">$("#id").text</code> instead of <code class="codespan">$("#id").text()</code> you will get some bizarre code that shows a function.

In the line <code class="codespan">success: function(msg)</code> the msg parameter will contain the text that you would normally see on a normal postback.

Thanks to SQLDenis, AlexCuse and Remou for proofreading this article.