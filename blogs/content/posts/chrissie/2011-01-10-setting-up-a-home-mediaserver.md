---
title: Setting up a home mediaserver
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-10T17:51:00+00:00
ID: 1000
excerpt: "A few weeks back I bought a Western Digital TV Live it's an amazing little machine. I connected it to my home network via a wire. A week after that I bought a second one to put in my bedroom and I connected that one wireless. But I'm not going to write&hellip;"
url: /index.php/sysadmins/hardware/setting-up-a-home-mediaserver/
views:
  - 2820
categories:
  - Hardware

---
A few weeks back I bought a [Western Digital TV Live][1] it&#8217;s an amazing little machine. I connected it to my home network via a wire. A week after that I bought a second one to put in my bedroom and I connected that one wireless. But I&#8217;m not going to write a review of that little machine. I have a [QNAP TS-210 NAS][2] with 2 terabytes to put all my movies on. 

The biggest challenge however was not to set all this up. The biggest challenge is and was to rip the DVD&#8217;s to the NAS. Apparently I have to many DVD&#8217;s. I thought I had 400 discs but I must have over 800 legally bought discs. Series like Star trek the Next generation alone take up 40+ discs and I have all star trek series and the stargate series and Babylon 5 and &#8230; 

So I started out to convert them with [Clonedvd2][3] and converted them to ISO in 5GB format. If you do the math (something I didn&#8217;t this means you can have 200 dics on 1 terabyte. Ripping them in ISO is pretty fast since I have 3 DVD players in my desktop and I could Rip from all three at the same time which means I can do 3 every half an hour. So I was merely on my way doing this when it turned out that after the above mentioned series I was already running out of diskspace on one of the disks. Darn. And the amount of diskspace is not the only inconvenience of ISO. ISO also keeps the disc-structure this means that you can not stream it. This meant that the wireless connection turned out to be to slow. Since you are not only have to get the data over but you need to get the data over fast enough for the little machine to decode and it seems that the wireless connection just does not have enough bandwidth to this. Not even the 150MB N. 

So I looked for another solution. [MKV][4] is a very good format (container actually), free and you can stream it. So I set about searching for a solution that is free and good. First thing I found was Handbrake. Handbrake is very cool.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ripping/handbrake1.png?mtime=1294686078"><img alt="" src="/wp-content/uploads/users/chrissie1/ripping/handbrake1.png?mtime=1294686078" width="1018" height="621" /></a>
</div>

The advantage of handbrake is that you can queue your jobs and that it is easy to use. When you use H.264 format the files will be about 60% the size of the ISO(MPEG2) files. The disadvantages are that it takes a long time to rip a disc, about 2 hours per disc, it uses a lot of CPU power while doing it and you can only do one disc at a time. The slowness is however because it needs to recode everything and that takes time and effort. 

I also tried [makeMKV][5]. It is also free. It does however not recode. It just transcodes to mkv using the same MPEG2 codec as the DVD. Which means the files are just as big as the original. But the transcoding is fast and you can do more than one at a time. It however does not let you name the titles to a nema you like so you need to rename them all afterward.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/ripping/handbrake1.png?mtime=1294686078"><img alt="" src="/wp-content/uploads/users/chrissie1/ripping/makedvd1.png?mtime=1294686078" width="1018" height="621" /></a>
</div>

I think this might be illegal in some countries but it falls under the fair use/backup for personal use laws in other countries.

 [1]: http://www.wdc.com/en/products/products.aspx?id=330
 [2]: http://www.qnap.com/pro_detail_feature.asp?p_id=135
 [3]: http://www.slysoft.com/nl/clonedvd.html
 [4]: http://www.matroska.org/
 [5]: http://makemkv.com/