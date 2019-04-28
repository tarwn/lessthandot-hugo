---
title: Microsoft reporting weirdness
author: Christiaan Baes (chrissie1)
type: post
date: 2010-05-05T11:53:31+00:00
ID: 781
url: /index.php/desktopdev/mstech/microsoft-reporting-weirdnes/
views:
  - 1478
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - rdlc
  - reportviewer
  - vb.net

---
I had some reporting weirdness going on today. Mostly because I don&#8217;t really know what I&#8217;m doing.

So, I had this problem.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/reportviewer/Reportweirdness1.png" alt="" title="" width="487" height="181" />
</div>

Can you see the gap under FiberColor? Why is that there?

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/reportviewer/Reportweirdness2.png" alt="" title="" width="643" height="220" />
</div>

As you can see in the above it was not visibly there in the designer.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/reportviewer/Reportweirdness4.png" alt="" title="" width="647" height="196" />
</div>

Untill I went into the textbox to edit it. then you see this.
  
The problem is that I had a few to many linebreaks in there. And I had the CanGrow property of that textbox set to true (which is the default). Setting the CanGrow to false will resolve the problem or better yet, you should remove the linebreaks in the text.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/reportviewer/Reportweirdness3.png" alt="" title="" width="483" height="119" />
</div>

And then everything worked as expected.