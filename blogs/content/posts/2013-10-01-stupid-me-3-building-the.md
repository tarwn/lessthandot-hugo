---
title: 'Stupid me #3 – Building the script component'
author: Koen Verbeeck
type: post
date: 2013-10-01T13:05:00+00:00
ID: 2162
excerpt: |
  After over 6 months of silence, it is time again for another Stupid me™®©! For those unfamiliar with the concept:
  Every time I do something "stupid", which happens from time to time, I'll do a little blog post on what happened and how I solved it. The&hellip;
url: /index.php/datamgmt/ssis/stupid-me-3-building-the/
views:
  - 150710
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - SSIS
tags:
  - .net
  - 'c#'
  - script component
  - ssis
  - syndicated

---
<p style="text-align: justify;">
  After over 6 months of silence, it is time again for another Stupid me™®©! For those unfamiliar with the concept:
</p>

<p style="text-align: justify;">
  <em>Every time I do something "stupid", which happens from time to time, I'll do a little blog post on what happened and how I solved it. The reason for this is twofold: I'll have a solution online I can consult if it happens again and other people can benefit from my mistakes as well. Because remember the ancient Chinese proverb</em>: <em>"It's only stupid if you don't turn it into a learning experience". Okay, I might have made that last one up...</em>
</p>

<p style="text-align: justify;">
  <strong>The problem</strong>
</p>

<p style="text-align: justify;">
  From time to time I have to script out some logic using a Script Component in a SSIS package. Business as usual for most developers, but since I hardly have a .NET background, it's always a very stressful experience (I may be over exaggerating a bit). Sometimes it happens I write a little piece of code with a small bug, it happens to the best of us.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/StupidMe3/introducebug.PNG?mtime=1380632628"><img src="/wp-content/uploads/users/koenverbeeck/StupidMe3/introducebug.PNG?mtime=1380632628" alt="" width="540" height="120" /></a>
</p>

<span style="text-align: justify;">For those who haven't noticed: you cannot store -1 in a byte. Anyway, the wonderful designer that is the Visual Studio shell fails to warn me of my little indiscretion. Leaving me with a script component that won't compile.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/StupidMe3/nocompile.PNG?mtime=1380632628"><img src="/wp-content/uploads/users/koenverbeeck/StupidMe3/nocompile.PNG?mtime=1380632628" alt="" width="676" height="212" /></a>
</p>

<span style="text-align: justify;">The matching errors aren't helpful either.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/StupidMe3/errors.PNG?mtime=1380632628"><img src="/wp-content/uploads/users/koenverbeeck/StupidMe3/errors.PNG?mtime=1380632628" alt="" width="853" height="307" /></a>
</p>

<span style="font-weight: bold; text-align: justify;">The solution</span>

<p style="text-align: justify;">
  The solution is elegant yet simple: we force the designer to tell us what's wrong by building the code inside the script component.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/StupidMe3/button.PNG?mtime=1380632628"><img src="/wp-content/uploads/users/koenverbeeck/StupidMe3/button.PNG?mtime=1380632628" alt="" width="467" height="125" /></a>
</p>

<span style="text-align: justify;">This results in a blue squiggly being added to the code, together with a decent explanation.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/StupidMe3/buildscript.PNG?mtime=1380632628"><img src="/wp-content/uploads/users/koenverbeeck/StupidMe3/buildscript.PNG?mtime=1380632628" alt="" width="559" height="227" /></a>
</p>

<span style="text-align: justify;">The moral of this story: if the script component is giving you a hard time, hit that build button!</span>