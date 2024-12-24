---
title: The Hanselminutes podcast player for Android 3.2
author: Christiaan Baes (chrissie1)
type: post
date: 2011-12-05T20:18:00+00:00
ID: 1419
excerpt: "Today I had some free time and decided to write another Android application. I didn't really know what to write until I noticed the new podcast from Scot Hanselman featuring Scott Reynolds. So I thought it would be fun to write an app for the Hanselminutes podcast series, just because I can."
url: /index.php/desktopdev/suntech/java/the-hanselminutes-podcast-player-for/
views:
  - 3598
categories:
  - Java
tags:
  - android
  - hanselminutes
  - podcast

---
## Introduction

Today I had some free time and decided to write another Android application. I didn&#8217;t really know what to write until I noticed the new podcast from Scot Hanselman featuring Scott Reynolds. So I thought it would be fun to write an app for the Hanselminutes podcast series, just because I can. Before you ask, Yes the source is available [on Github][1].

## The now

What does the application do for now.

It gets a list of all podcasts when you click on the refresh list button.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer1.png?mtime=1323122822"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer1.png?mtime=1323122822" width="1070" height="669" /></a>
</div>

When you then select a podcast and click play it will show you which title you just selected and you can also pause an stop play. Simple really.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer2.png?mtime=1323122840"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/android/hanselminutesplayer2.png?mtime=1323122840" width="1070" height="669" /></a>
</div>

The mp3 is streamed so for now you need an internet connection to make this work.

## The future

Make the seekbar work a little better. It works but it&#8217;s quirky.
  
In the futue I want to save the list of podcasts in an SQLite database so that the user doesn&#8217;t have to download the list everytime. I want to also give the user the ability to download the mp3 so that you can listen to it when not connected.

## Sources

I used an [article on the IBM site][2] for the RSS-reader part.
  
And I used a [musicsample by krvarma on google code][3] for how to use the mediaPlayer part.

 [1]: https://github.com/chrissie1/HanselMinutesPlayer/tree/master/src/be/baes/hanselMinutesPlayer
 [2]: http://www.ibm.com/developerworks/xml/tutorials/x-androidrss/
 [3]: http://code.google.com/p/krvarma-android-samples/source/browse/trunk/MusicPlayer/src/com/varma/samples/musicplayer/