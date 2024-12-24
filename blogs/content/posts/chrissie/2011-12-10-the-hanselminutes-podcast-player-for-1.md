---
title: The Hanselminutes podcast player for Android 3.2 version 0.2
author: Christiaan Baes (chrissie1)
type: post
date: 2011-12-10T10:44:00+00:00
ID: 1429
excerpt: |
  In the beginning of the week I started writing the Hanselminutes player for Android 3.2 because it seemed like something I would learn from.
  And man did I learn.
url: /index.php/desktopdev/suntech/java/the-hanselminutes-podcast-player-for-1/
views:
  - 2226
categories:
  - Java
tags:
  - android
  - hanselminutes

---
In the beginning of the week I started writing the [Hanselminutes player for Android 3.2][1] because it seemed like something I would learn from.

And man did I learn.

First of all, it is still up on [github][2]. And I am now using [IntelliJ][3] instead of Eclipse. 

What I learned from version 0.2

  * Endless paging with a listview.
  * AsyncTasks
  * Adding progressdialogs
  * Adding a contextmenu

What did add in Version 0.2

  * The list is now cached in an SQLite database.
  * The List is paged.
  * Shows the total number of podcasts and the loaded number of podcasts.
  * Multithreaded loading of podcasts
  * Multithreaded refreshing od podcasts from rss feed.
  * An icon based on the Hanselminutes logo (with permission from Scott).
  * Ability to delete the cache.
  * Contextmenu for the listview.

What&#8217;s up for version 0.3.

Save the MP3 in the database so you don&#8217;t need an internetconnection to listen to them.

The screenshots.

A complete view.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer3.png?mtime=1323520708"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer3.png?mtime=1323520708" width="1070" height="669" /></a>
</div>

When refreshing the list a progressdialog appears.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer4.png?mtime=1323520718"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer4.png?mtime=1323520718" width="729" height="283" /></a>
</div>

When scrolling down the list will load more elements when needed.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer5.png?mtime=1323520736"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer5.png?mtime=1323520736" width="813" height="248" /></a>
</div>

When deleting all the items out of the cache or when you open the application.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer6.png?mtime=1323520746"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer6.png?mtime=1323520746" width="1070" height="669" /></a>
</div>

The contextmenu pops up when you longclick.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer7.png?mtime=1323520757"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer7.png?mtime=1323520757" width="718" height="302" /></a>
</div>

That&#8217;s it for now.

 [1]: /index.php/DesktopDev/SunTech/Java/the-hanselminutes-podcast-player-for
 [2]: https://github.com/chrissie1/HanselMinutesPlayer
 [3]: http://www.jetbrains.com/idea/