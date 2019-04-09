---
title: Fun project in Silverlight
author: ThatRickGuy
type: post
date: 2009-09-18T17:17:03+00:00
ID: 562
url: /index.php/webdev/webdesigngraphicsstyling/fun-project-in-silverlight/
views:
  - 5990
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling

---
So I'm finally writing a blog post. Not that I'm a lazy guy or anything, but I doubt anyone here wants to read about the stupid things my wife's cats do or what I thought of the last block buster movie that came out. But I finally have a few things that I think raise to the bar of &#8216;worth showing other people'.

I've been lucky, in my current environment I have a pretty loose leash and I get to push different technologies that would otherwise be ignored. And for the last 2 years I've been following and pushing for traction on Silverlight. To put it bluntly, I love Silverlight. Oh sure, it's got its fair share of faults. But it is an amazing platform and keeps getting better. 

This post though, is just for fun. A few days ago, Denis started a discussion about [35 Examples of Masterful Lighting Effects in Web Design][1]. One of the sites from the contained link is <http://www.rareview.com/>. I was rather impressed by their flash banner, so I figured I'd try to make something similar in Silverlight. 

After about 2 hours of work (a lunch and some free time), I came up this:

[![][2]][3]

It's not nearly as refined as theirs, but it has all the same concepts. 

The imagery is actually pretty simple. I have three PNGs I created in photoshop. The color layer and two cloud layers. The color layer was made by putting bold colors in vertical stripes on an 800&#215;800 canvas, then applying a horizontal motion blur and cropping it down to a circle. 

The clouds started out as a cloud filter on a 2400&#215;2400 canvas. I used the magic wand to select the darkest pixel with set to non-continuous, anti-aliased, and a 120 tolerance. I used the Select -> Feather option to feather the selection out 100 pixels then deleted. Then I re-sized the canvas down to 800&#215;800 and cropped it into a circle. Then I repeated this process to create a second cloud layer.

From there I hopped into Blend and tossed a couple of rectangles onto a grid. I set them to stretch alignment and auto height/width. Turned off the stroke and set the fill brush to the images I had just created. I then created an animation. In it I applied a rotate transform to both of the cloud rectangles. One set to 360, the other set to -360, and the duration set to 60 seconds with repeat set to Forever.

Jumping into the code behind, I just had to add animation.Begin() to start it up. And that was that!

I put more work in after that playing with the opacity of the clouds and different backgrounds. And adding the particle effect and resizing, but getting that first part working, and having it be so easy was what really excited me.

-Rick

 [1]: http://forum.ltd.local/viewtopic.php?f=7&t=5764&start=0&st=0&sk=t&sd=a
 [2]: http://ringdev.com.web10.reliabledomainspace.com/images/tm_cloudwheel.png "Click for SL Demo"
 [3]: http://ringdev.com.web10.reliabledomainspace.com/code/spaceeffect/SL_CloudWheelTestPage.aspx