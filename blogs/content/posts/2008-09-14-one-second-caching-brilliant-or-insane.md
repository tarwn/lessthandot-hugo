---
title: One Second Caching, Brilliant Or Insane?
author: SQLDenis
type: post
date: 2008-09-14T16:18:00+00:00
excerpt: "Let's say you get 1000 hits per second on your website's home page, you want the users to feel that the site is as real time as possible without overloading your database. These hits on your site display some data from the database and are mostly reads;&hellip;"
url: /index.php/webdev/webdesigngraphicsstyling/one-second-caching-brilliant-or-insane/
views:
  - 5406
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - apache
  - caching
  - database
  - iis
  - performance

---
Let&#8217;s say you get 1000 hits per second on your website&#8217;s home page, you want the users to feel that the site is as real time as possible without overloading your database. These hits on your site display some data from the database and are mostly reads; for every 10 reads there is one write. 

Yes you can cache it for 1 minute and the database won&#8217;t be hit that much. By doing this it will look like your site doesn&#8217;t update so often. Now what will happen if you do a 1 second cache? You will eliminate most of the reads and your site will still look like it is updating real time. If a user hits F5/CTRL + R every couple of seconds he will see new content. Does this make sense? I am interested in your opinion.

[edit]I am talking about database results caching here. For example take a page like the new submission page on digg, a lot of stuff gets submitted every second, a lot more gets read of course. If you cache this for one second you eliminate a lot of the reads[/edit]