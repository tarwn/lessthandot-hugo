---
title: Excel and the divide by 100 problem
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-16T11:35:49+00:00
ID: 431
url: /index.php/desktopdev/mstech/excel-and-the-divide-by-100-problem/
views:
  - 4248
categories:
  - Microsoft Technologies
  - VBA for Microsoft Office Products
tags:
  - 2003
  - excel
  - office

---
If you ever get the question from someone why excel is dividing the number by 100 for no aparent reason, Well then I have the answer 😉

In Excel 2003 goto <span class="MT_blue"><code class="codespan">Tools > Options > Edit > Fixed decimal</code></span> places. Chances are that that little checkbox is checked. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/exceldecimal%20places.jpg" alt="" title="" width="498" height="398" />
</div>

In excel 2007 they changed the description (automaticaly insert a decimal point) for that checkbox and changed it&#8217;s location to <span class="MT_blue">Office Button > Excel Options > Advanced</span>. Thanks to George for checking this.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/FixedDecimals.png" alt="" title="" width="840" height="685" />
</div>

I&#8217;m sure that you can play tricks on people with that. I know of not many people that know what that option does. Including myself until a few hours ago. Took me half an hour to find it. 

I actualy wonder who uses that option anyway. and George to the rescue again. 

> I suppose&#8230;.. if you are entering a lot of data (like money with 2 fixed decimal places), it would be faster to enter the data without the point. $12.42, just type 1242. 

Of course won can wonder if you really have the rigth tool with excel if that is going to make a difference.