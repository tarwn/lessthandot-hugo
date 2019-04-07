---
title: Enabling ReadyBoost In Vista
author: SQLDenis
type: post
date: 2009-06-07T16:47:18+00:00
ID: 459
url: /index.php/sysadmins/os/enabling-readyboost-in-vista/
views:
  - 4055
rp4wp_auto_linked:
  - 1
categories:
  - Operating Systems
tags:
  - performance
  - readyboost
  - vista

---
So I just got a brand new shiny QuadCore PC with 6GB of RAM and Vista on it. I decided to setup ReadyBoost since I have a couple of USB dongles laying around. 

How does ReadyBoost work? Here is what [Wikipedia][1] has to say

> Using ReadyBoost-capable flash memory (NAND memory devices) for caching allows Windows Vista to service random disk reads with performance that is typically 80-100 times faster than random reads from traditional hard drives. This caching applies to all disk content, not just the page file or system DLLs. Flash devices typically are slower than a hard disk for sequential I/O so, to maximize performance, ReadyBoost includes logic that recognizes large, sequential read requests and has the hard disk service these requests

Here are the requirements for the USB Device

For a device to be compatible and useful it must conform to these requirements:

  * The removable media&#8217;s capacity must be at least 256 MB—250 MB after formatting. Windows Vista x86 is limited to using 3.5GB (Vista x64 can support up to 16GB); this restriction has been removed in Windows 7.
  * The device must have an access time of 1 ms or less.
  * The device must be capable of 2.5 MB/s read speeds for 4 KB random reads spread uniformly across the entire device, and 1.75 MB/s write speeds for 512 KB random writes spread uniformly across the device

If you device meets that then follow these steps to enable ReadyBoost.

First find your USB device. Go to my computer, right click on the device and select properties.

![right click on the device and select properties][2]

If your device is ReadyBoost enabled a tab named ReadyBoost will be visible

![a tab named ReadyBoost will be visible][3]

Finally check use this device, give it at least 640MB and you are ready to go

![ive it at least 640MB][4]

Some additonal info about ReadyBoost can be found here: http://www.microsoft.com/windows/windows-vista/features/readyboost.aspx

ReadyBoost will also be available in Windows 7

 [1]: http://en.wikipedia.org/wiki/Readyboost
 [2]: http://imgur.com/yGaRb.png
 [3]: http://imgur.com/4dPgq.png
 [4]: http://imgur.com/GEW84.png