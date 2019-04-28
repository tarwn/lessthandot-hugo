---
title: Fighting the automatic spam
author: Christiaan Baes (chrissie1)
type: post
date: 2010-12-31T12:55:00+00:00
ID: 992
excerpt: |
  Introduction
  The more popular your site becomes the more spam you will get. And a few weeks ago we had a lot of comment spam on this site. When fighting spam you must know what drives the enemy. And in this case it is backlinks.
  To moderate or not to&hellip;
url: /index.php/webdev/uidevelopment/ajax/figthing-the-automatic-spam/
views:
  - 1606
categories:
  - AJAX

---
## Introduction

<p style="text-align: center;">
   
</p>

<div class="image_block">
  <a href="/media/users/chrissie1/spam.jpg?mtime=1293807523"><img src="/wp-content/uploads/users/chrissie1/spam.jpg?mtime=1293807523" alt="" width="168" height="133" /></a>
</div>

 

The more popular your site becomes the more spam you will get. And a few weeks ago we had a lot of comment spam on this site. When fighting spam you must know what drives the enemy. And in this case it is backlinks.

## To moderate or not to moderate

Since our site still gets a normal amount of comments I think we should still moderate, this will keep manual spam to a minimum. Because you should not forget that once they get through your defenses they will flood you via that hole in your defense.

## The first attempt

My first attempt at fighting the spam was to remove the website field for the anonymous comments. I thought that that would negate the need for them to post on this site. What I did was to remove the website field from the html.

To my big surprise it did not. The spam kept coming. Why? Because most of the comment spam we got was automatic and they were still posting to that field. My backend code still used that parameter so I still got the comment.

## Second attempt

Since I now know that the automatic spam still post to this field by name I can just remove the spam automatically whenever somebody posts to that field name. I re-added the field with a different name this time.

Now we get a lot less spam. Until they find the name of that field which is not to hard to find.Which makes me think these spammer use some kind of common program that posts for them, and alas if you search the web you will find such software. SO as soon as this site gets even more popular and an even higher pagerank it will attract more specialized programs. But I&#8217;m guessing that will be some years from now.

## The future

In the future I want to make that field name semi-random or at least make it change on a regular basis. I could for instance add the month to it or the year or even the complete date of today. Perhaps even use an external comments service to outsource the whole thing.

## Conclusion

Fighting spam depends on the amount of traffic and what spam you get. It&#8217;s a fight against them and keeping a good UX.