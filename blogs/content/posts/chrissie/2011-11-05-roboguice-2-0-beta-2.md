---
title: RoboGuice 2.0 beta 2 and a notes application
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-05T18:32:00+00:00
ID: 1372
excerpt: A while ago I wrote about Roboguice. I used 1.1 way back then. Now I switched to version 2.0 beta 2. I also wrote a test application to see how it works. And here is my story.
url: /index.php/desktopdev/suntech/java/roboguice-2-0-beta-2/
views:
  - 3310
categories:
  - Java
tags:
  - 2.0
  - android
  - beta
  - roboguice

---
## Introduction

A while ago [I wrote about Roboguice][1]. I used 1.1 way back then. Now I switched to [version 2.0 beta 2][2].

## The changes

First of all we need a few new jars.
  
I found out the hard way that I needed 4.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/android/android7.png?mtime=1320523293"><img alt="" src="/wp-content/uploads/users/chrissie1/android/android7.png?mtime=1320523293" width="202" height="95" /></a>
</div>

You can find the latest beta [on maven][3].
  
You can find the Guice 3.0 noaop jar [on code.google.com][4].
  
The javax.inject.jar can also be found [on code.google.com][5].
  
And the [android v4 support jar][6] I found on java2s.

Then secondly, you no longer need to have a class inheriting from RoboApplication, which also means that you can remove the name (application tag) in AndroidManifest.xml. And your modules class no longer needs to inherit RoboModule anymore. It now inherits from AbstractModule.

And if you really need that module you can make a roboguice.xml file in the res/values folder.

After that everything went just fine, not really. But I got it to work in the end.

## The application

You can find my test application on [github][7]. It&#8217;s a notes Application and looks something like this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/android/android6.png?mtime=1320524663"><img alt="" src="/wp-content/uploads/users/chrissie1/android/android6.png?mtime=1320524663" width="507" height="835" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/android/android8.png?mtime=1320524705"><img alt="" src="/wp-content/uploads/users/chrissie1/android/android8.png?mtime=1320524705" width="509" height="827" /></a>
</div>

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/android/android9.png?mtime=1320524877"><img alt="" src="/wp-content/uploads/users/chrissie1/android/android9.png?mtime=1320524877" width="513" height="829" /></a>
</div>

It uses 3 different Activities, an SQLite database and RoboGuice of course. It has no tests yet and I wouldn&#8217;t call it MVC either. I made it for android 2.2. 

It uses @Inject and @InjectView. 

## Conclusion

The lack of documentation was killing my productivity for a bit, but I got there in the end. And I now understand how it works, more or less. Something to note when you come from .Net IoC containers is that Guice seems to prefer using property injection over constructor injection and that it lets you inject implementation instead of interfaces.

 [1]: /index.php/DesktopDev/SunTech/Java/using-roboguice-to-inject-your
 [2]: http://code.google.com/p/roboguice/wiki/UpgradingTo20
 [3]: http://repo1.maven.org/maven2/org/roboguice/roboguice/
 [4]: http://google-guice.googlecode.com/files/guice-3.0-no_aop.jar
 [5]: http://code.google.com/p/dependency-shot/downloads/detail?name=javax.inject.jar&can=2&q=
 [6]: http://www.java2s.com/Code/JarDownload/android/android-support-v4.jar.zip
 [7]: https://github.com/chrissie1/RoboGuiceNotes