---
title: Jquery a tooltip and the need for some ajax
author: Christiaan Baes (chrissie1)
type: post
date: 2009-12-31T13:40:18+00:00
ID: 659
url: /index.php/webdev/uidevelopment/ajax/jquery-a-tooltip-and-the-need-for-some-a/
views:
  - 6618
categories:
  - AJAX
tags:
  - ajax
  - jquery
  - tooltip

---
On this site we have a suggestions forum and [David suggested][1] that the authors list on this forum could do with a summary.

> In an Author&#8217;s list of blogs, have a mouse-over effect that pops up the first 1-2 paragraphs of the blog.

First, I thought that Ajax and jquery is nothing for me. Web development is not something I like doing and I did plenty of it for the site during my 5 week vacation this year. But the challenge intrigued me. So I started a little google and did a bit of experimenting. 

The first one I tested was a very simple one to implement. [The Cut & Paste Ajax tooltip][2] and it worked like a charm. But it had a little bug in it. The tooltip would not always go away when moving the mouse too fast, which was a bit annoying.

So I went on the lookout for another one. And I found [cluetip][3], which was easy to implement and very easy to change.

First thing I did was to download the scripts and images and add them to my project. Then I added the needed code to my head section.

```html
&lt;script src="jquery.js" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="jquery.hoverIntent.js" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="jquery.cluetip.js" type="text/javascript"&gt;&lt;/script&gt;```
I made sure all the relevant links had the same class, in this case cluetip.
  
Then I added a short piece of javascript to the page to make it work.

```
&lt;script type="text/javascript"&gt;
$(document).ready(function() {
  $('a.cluetip').cluetip();
});
&lt;/script&gt;```
Now you have to add a rel attribute to the link so that the tooltip knows where to get its data.

For me it would now look something like this. 

```
&lt;a class="cluetip" rel="/htsrv/call_plugin.php?plugin_ID=17&method=excerpt&params=a%3A1%3A%7Bs%3A6%3A%22postid%22%3Bs%3A3%3A%22628%22%3B%7D" title="Validating a domain model/objects" href="http://blogs.dev.ltd.local/index.php/All/?p=628"&gt;Validating a domain model/objects&lt;/a&gt;```
As you can see, the link also has a title which will be used as a title in the tooltip. 

YOu can see it in action on my [authorslist][4].

There are some settings that can be changed. Well, [there are a lot of settings that you can change][5].

You can either change them by changing the default in the jquery.cluetip.js file or by overriding them locally. Like so:

```
&lt;script type="text/javascript"&gt;
$(document).ready(function() {
  $('a.cluetip').cluetip({width:'350px');
});
&lt;/script&gt;```
Here I override the width from the standard 220px to be 350px. 

or:

```
&lt;script type="text/javascript"&gt;
$(document).ready(function() {
  $('a.cluetip').cluetip({width:'350px',clickThrough: true);
});
&lt;/script&gt;```
clickThrough is standard on false but I think that is a mistake since links should always be clickthrough.

BTW the authorslist uses the jTip theme and not the standard theme.

All in all, very simple to use even for a noob like me. And instant wow factor to the site. Even more reason for people to think I&#8217;m awesome ;-).

 [1]: http://forum.ltd.local/viewtopic.php?f=53&t=9263
 [2]: http://www.javascriptkit.com/script/script2/ajaxtooltip.shtml
 [3]: http://plugins.learningjquery.com/cluetip/
 [4]: /index.php/All/?disp=authdir&author=7
 [5]: http://plugins.learningjquery.com/cluetip/#options