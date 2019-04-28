---
title: Installing windows 7 on an Acer aspire One 751h
author: Christiaan Baes (chrissie1)
type: post
date: 2009-06-24T18:26:11+00:00
ID: 480
url: /index.php/sysadmins/os/installing-windows-7-on-an-acer-aspire-o/
views:
  - 13493
categories:
  - Hardware
  - Operating Systems
  - Windows
  - XP
tags:
  - acer
  - windows 7

---
This week I bought a new netbook. The [Acer Aspire One 751h][1] (what&#8217;s in a name). It&#8217;s an amazing little machine, but I guess everything is amazing at my age I can still remember things like the Commodore 64 and the first x86. 

![Acer aspire one][2]

Standard, it only comes with 1Gb of RAM but it supports a maximum of 2GB so I ordered that with it and installed it myself which is pretty simple no need to spend 20€ on a tech to do it for you. 

The machine came with Windows XP SP3 and it was pretty fast with it. So to start with, I installed Firefox, thunderbird and openoffice like I always do on my machines. This machine is special of course, since it is not that fast and I was not planning on installing VS on it. But after working with it for a few days I decided to install something different on it. Before buying it I planned on installing Ubuntu on it. But I guess I like Windows better and I installed Windows 7 on it.

Windows 7 RC is pretty stable in my VM&#8217;s so it was worth the risk. First thing to do was to decide how to do it since the netbook has no optical drive I guess USB was the next option. I hooked up an external HD drive and unzipped the iso file. 

I was planning on upgrading but Windows 7 does not really upgrade from XP. You have to use the Windows easy transfer wizard for this. It is all explained very well here http://technet.microsoft.com/en-us/library/dd446674(WS.10).aspx apart from the fact that you have to reinstall your soft. I don&#8217;t like wizard most of the time but this easy transfer thing is really easy and it decided that I had 2,5GB of data to transfer. I wonder how I did that? I only used it for a couple of days. Anyway, I could then start installing Windows 7 which took less than half an hour and would have been even less if I had remembered to write down the serial number somewhere. Everything installed just fine, even the graphics card which had me worried for a while but Windows 7 decided to install that after the last reboot (yes it still needs reboots to install everything, 4 if I remember correctly but I wasn&#8217;t really counting).

After the install was finished I had to run the easy transfer wizard again. But the easy transfer wizard only contains the data not the programs. So I had to reinstall the programs with the added bonus that after installing all programs I had, it seemed to have remembered their settings. Firefox still had all the plugins I installed and thunderbird knew my settings too.

So all in all, this was a very easy thing to do.

 [1]: http://www.acer.co.uk/acer/product.do?link=oln85e.redirect&changedAlts=&CRC=600100215#wrAjaxHistory=0
 [2]: http://www.acer.co.uk/acer/wr-resource/3272001591/upload/E0Entity3/1/ASONE_751.jpg "Full view acer aspire one"