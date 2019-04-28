---
title: How to flash your bios with an Asus P5E MB and NO floppy disk
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-12T17:43:06+00:00
ID: 138
url: /index.php/sysadmins/os/how-to-flash-your-bios-with-an-asus-p5e/
views:
  - 4137
categories:
  - Hardware
  - Operating Systems
  - Windows
tags:
  - asus
  - bios
  - flash

---
Today I bought a brand new [Intel Q9550][1] processor. My P5E Motherboard had a Bios version of 0203, and it told me ever so kindly that it didn&#8217;t recognize the new proc and the Bios needed a little flash. So I ignored that 😉 and continued on my way and booted in to Vista. And to be honest, it did boot into Vista, it even let me do some work for a while. 

And then&#8230; after 20 minutes it crashed and no way to get it to boot again. Lucky for me I still had my old proc and I booted with that again. I then searched the ASUS site for the new Bios and I found [version 0903][2], so I downloaded that. And then I looked up the [howto for this flash business][3]. And to my surprise, I needed a floppy disk for that.
  
I wonder what these guys at ASUS are thinking about? They use floppy disks to flash their Bios? My computer still has the floppy drive but I no longer have any floppy disks. And why should I? The things never worked when you needed them, anyway. So I had to find another way. I thought about using a USB drive to boot from. And after a while I found a site that explains very well on [how to make a bootable DOS USB drive the easy way][4] (there are lots of hard ways). I did that.
  
And I copied the rom file and the little flash program to the USB-drive and rebooted. I had to set the USBdrive to emulate a hard disk and I had to set it to boot first. Then I had to delete the autoexec.bat and the config.sys from the drive and boot again, else it will say that it can only run in dos mode. After some exciting moments, the Bios was flashed and everything worked well.

The PC has been up for over an hour now and I&#8217;m even typing this post from it. So next time they tell you to use a floppy, prove them wrong and use the usb drives it is much better for my heart.

 [1]: http://ark.intel.com/cpu.aspx?groupID=33924
 [2]: http://support.asus.com/download/download.aspx?SLanguage=en-us&model=P5E
 [3]: http://support.asus.com/technicaldocuments/technicaldocuments.aspx?root=198&SLanguage=en-us
 [4]: http://www.bay-wolf.com/usbmemstick.htm