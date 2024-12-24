---
title: Pure randomness or bad caching
author: Christiaan Baes (chrissie1)
type: post
date: 2011-06-18T05:22:00+00:00
ID: 1225
excerpt: |
  Introduction
  
  Last weekend I added a few social buttons to our blogsection. I added the reddit button, the facebook button and a google +1 button. We already had the twitterbutton in there (Tarwn added that).
  
  The problem
  
  The problem is that some&hellip;
url: /index.php/itprofessionals/ethicsit/pure-randomnes-or-bad-caching/
views:
  - 1799
categories:
  - Ethics and IT
tags:
  - caching
  - reddit
  - twitter

---
## Introduction

Last weekend I added a few social buttons to our blogsection. I added the reddit button, the facebook button and a Google +1 button. We already had the twitterbutton in there (Tarwn added that).

## The problem

The problem is that some of these buttons have some majors problems and there is nothing you can do about it. Their numbers are just wrong even after days of inactivity and differ from computer to computer. This might be caused by caching or bad alignment of datacenters but as a user I really don&#8217;t care. It&#8217;s bad either way.

## Twitter

The official twitter button is amazingly bad at giving the correct numbers or even something remotely accurate. For me it even gives different results on different browsers. 

Here on firefox 4. Which gives me 0, 3 and 0

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/twitterbutton1.png?mtime=1308380894"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/twitterbutton1.png?mtime=1308380894" width="1252" height="579" /></a>
</div>

And on IE9. Which gives me 10, 6 and 4.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/twitterbutton2.png?mtime=1308380903"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/twitterbutton2.png?mtime=1308380903" width="1252" height="625" /></a>
</div>

I don&#8217;t know why this is but as a user of there system this makes it completely unusable and a lie.

## Reddit

And just look at the reddit numbers. You might have noticed in the numbers above that FF gives an 8 and IE a 9, but it&#8217;s much worse than that.

Just go to [this page][1] and click refresh a few times. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/reddit1.png?mtime=1308381323"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/reddit1.png?mtime=1308381323" width="316" height="101" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/reddit2.png?mtime=1308381333"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/reddit2.png?mtime=1308381333" width="304" height="101" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/reddit3.png?mtime=1308381341"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/reddit3.png?mtime=1308381341" width="308" height="104" /></a>
</div>

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/reddit4.png?mtime=1308381350"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/buttons/reddit4.png?mtime=1308381350" width="308" height="102" /></a>
</div>

Every time you click refresh you get a different number. Which is insane. 

## Conclusion

I can believe that sites like reddit and twitter have lots of traffic and they feel the need to cache. But they are caching it to death or just inventing numbers. Just look at a site like Stackoverflow who have about the same traffic and also cache a lot. They don&#8217;t get that quirkiness, they give you more or less reliable numbers. And so do Google +1 and facebook (as far as I can tell). Rant over.

 [1]: http://www.reddit.com/r/node/comments/hz4xm/nodejs_using_mustachejs_for_templating/