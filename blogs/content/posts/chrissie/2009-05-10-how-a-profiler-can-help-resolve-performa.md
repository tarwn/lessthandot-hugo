---
title: How a profiler can help resolve performance problems.
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-10T06:35:03+00:00
ID: 414
url: /index.php/desktopdev/mstech/how-a-profiler-can-help-resolve-performa/
views:
  - 2943
categories:
  - Microsoft Technologies
tags:
  - .net
  - dottrace
  - profiler
  - profiling

---
Today I finaly had some time to sacrifice to finding why my application was slow at startup (around 30 seconds). So I opened up [JetBrains&#8217;s dotTrace][1] and went to work. In less then half an hour I shaved at least 10 seconds of the startup time.

So here is the first profiler screenshot.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/profiler/profiler2.jpg" alt="" title="" width="911" height="265" />
</div>

Here we can see that the main method takes 20 seconds to run and that <code class="codespan">resolve&lt;T&gt;</code> takes the most time.

Now lets drill down and see if we can find one that takes up most of that time.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/profiler/profiler1.jpg" alt="" title="" width="561" height="135" />
</div>

As we can see FindAllPimsCases lin LoadList takes up 9 seconds of my time and it&#8217;s not even needed (it&#8217;s a connection with a very slow Oracle database). So I delete it. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/profiler/profiler3.jpg" alt="" title="" width="962" height="301" />
</div>

And the result is above. Main now takes 11 seconds to run. Which is acceptable for now.

 [1]: http://www.google.be/url?sa=t&source=web&ct=res&cd=1&url=http%3A%2F%2Fwww.jetbrains.com%2Fprofiler%2F&ei=kp0CSrCtLce4-Qae_LCRAw&usg=AFQjCNGbmcIU7aPIZYLu465RUUms0Xaw6Q&sig2=XAhZFpwn_QSrS5w_Wx1bhA