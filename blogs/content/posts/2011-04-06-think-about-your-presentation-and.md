---
title: Think about your presentation and who reviews it
author: Ted Krueger (onpnt)
type: post
date: 2011-04-06T11:00:00+00:00
ID: 1103
excerpt: 'One of my new friends that I was fortunate to make while attending the MVP Summit, Dan English (Twitter | Blog | MVP), mentioned last night something that hit home pretty hard and fast.  I was writing an SSIS package to go along with one of my SQL Unive&hellip;'
url: /index.php/datamgmt/datadesign/think-about-your-presentation-and/
views:
  - 4018
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Microsoft SQL Server
  - SSIS

---
One of my new friends that I was fortunate to make while attending the MVP Summit, Dan English ([Twitter][1] | [Blog][2] | [MVP][3]), mentioned last night something that hit home pretty hard and fast.  I was writing an SSIS package to go along with one of my [SQL University][4] posts and posted an image of my first initial execution of the package.  See, all of the boxes were green on that first try and it’s always a smile when you get all green boxes on your first run.  But what if you can’t see the color green?

Dan’s reply to my tweet:

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-24.png?mtime=1302094637"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-24.png?mtime=1302094637" width="312" height="82" /></a>
</div>

This is something I had never considered and I will be very honest in saying that.  After reading this tweet, it hit me that I really should have thought about this a long time ago and been very proactive about it. 

What Dan mentions in the tweet is the labels we can turn on for precedence constraints.  He also has a great blog that goes over this, “[Integration Services (SSIS) Package Designing Tips][2]”.  I realize just how important this is so I’m going to put it up here as well.

Simply stated; turning precedence labels on will show precedence as success, failure and completion will be written next to the precedence connections.  (See below)

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-25.png?mtime=1302094638"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-25.png?mtime=1302094638" width="759" height="440" /></a>
</div>

So set this option on go to Tools a Options and select Business Intelligence Designers.  Tick the Accessibility option for “Show precedence constraints labels.

<div class="image_block">
  <a href="/wp-content/uploads/blogs/DataMgmt/-26.png?mtime=1302094638"><img alt="" src="/wp-content/uploads/blogs/DataMgmt/-26.png?mtime=1302094638" width="821" height="678" /></a>
</div>

Now when the package executes, the tasks that run and the flow will be easier to distinguish when green or red may not be accessible. 

From Dan’s blog there is also mention of naming your objects in SSIS with something meaningful.  I’m guilty in some of my blogs and articles where I have not done this.  I would like to say I could just go with the saying, “Do what I say, not what I do”.  But I won’t.  In the packages you write for work, presentations, blogs, articles or even a funny image you tweet on twitter; make sure you name them appropriately so it makes sense when someone looks at it. 

I also suggest changing the objects so the text is bold for presentation purposes along with the use of ZoomIt.

Thanks goes to Dan for bringing this up and making me a better presenter from it.

 [1]: http://twitter.com/denglishbi
 [2]: http://denglishbi.wordpress.com/2009/02/01/integration-services-ssis-package-designing-tips/
 [3]: https://mvp.support.microsoft.com/profile=549CC424-32DC-47C9-ADFF-AC234E0C89BB
 [4]: http://sqlchicken.com/sql-university/