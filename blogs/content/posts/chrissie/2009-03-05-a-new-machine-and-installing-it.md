---
title: A new machine and installing it
author: Christiaan Baes (chrissie1)
type: post
date: 2009-03-05T19:11:48+00:00
ID: 340
url: /index.php/sysadmins/hardware/a-new-machine-and-installing-it/
views:
  - 9334
categories:
  - Hardware
tags:
  - hardware

---
Last week I got a new computer at work. A real beast I might add. I already wrote about [what I wanted][1] a while ago. But after a nice email conversation with the [hardware supplier][2] it actually became a bit better than that.

Let&#8217;s start with the case. A [Coolermaster Cosmos 1000][3].

![Coolermaster Cosmos 1000][4]

This is a wonderfull case, nice and big and nice and quiet and nice and cool. Even with 5 fans in it you hardly hear it.

Then I needed a Motherboard to plug the important things into. It became the [Asus P6T WS pro workstation][5] a mouthfull but it had everything I needed and wanted. 

![Asus W6T pro workstation][6]

A computer needs lots of ram in my opinion. So I was going for 8Gb but this MB need Triple channel DDR3 and that&#8217;s what I put in it. Triple channel means 3 bars of the same or 2&#215;3 ;-). SO I choose to get 2x 3x 2 Gb the [Corsair variety][7]. 

Then I also needed a nice processor. I think the i7 would be nice, but budget only allowed for a [920][8] :-(. What can I say&#8230; it works. 

Now we need something to put all that power to the screen. 3 monitors to be exact. So 2 Graphics cards. Nothing special I don&#8217;t need 3D power I need 2D power. So a [cheapo 9800 GT][9] will do.

![9800 GT][10]

See it also has a nice big fan, that&#8217;s 2 more. 

For the Electrical things I went for the [OCZ HIGH PERFORMANCE PSU [NVIDIA SLI READY] GameXStream 600W][11] it has a nice blue light ;-). 

![OCZ Power supply][12]

And it ads fan number 8 tot the system, perhaps I should get some chains to keep it on the ground ;-). 

What else do we need&#8230; Aha DVD player. Nothing special some cheapo stuff. I won&#8217;t even mention the brand because I already forgot.

And then we also need some hard drive space. First I wanted speed for the OS and my source code. So I went for SSD namely 2 of these [OCZ Core Series V2 Solid State Drive 2,5&#8243; &#8211; 60GB &#8211; SATA-2 &#8211; 170MB/s rd 98MB/s wr][13]. And 2 normal seagate 250 Gb disks for storage in Raid 0 for a bit of extra speed. This is to store the VM files.

Did I forget something? I don&#8217;t think so. So I installed all this in a couple of hours, unpacking all this takes longer then putting it together and the amount of garbage is shameful. I never got any blood from unpacking all of this and I think that was a first. The 2,5 inch SSD&#8217;s needs some brackets to get it into the case but that was forseen by the suplier not OCZ which is a shame. The only big problem I had was with the second GC it is very close to the bottom and there is only limited space between the external USB connections and the GC fan. So the first time I started the machine it started just fine and even worked (I think that was a first too ;-)) But after about ten minutes it started acting strange it started getting in a continous reboot cycle. I know that can happen from overheating so I let it rest ten minutes and then booted again, it wasn&#8217;t the proc because according to the BIOS it was running at normal temp a bit high perhaps but nothing extraordinary. And only a minute later it also started to reboot again so I had to look a bit closer. And guess what? It was the second GC and the fan was blocked by the cable going to the USB connection. So I fixed that and had to wait a few minutes for it to cool down because it was REAL hot. 

After that everything went fine and I installed Vista 64 bit on it without much problems apart form the fact that it refused to install on the SSD the first few times and the fact that it at first refused to boot from the DVD. The part about the DVD is actualy still a problem which I can&#8217;t really solve. I think it has to do with the fact that the computer boots to fast and that the DVD doesn&#8217;t spin up fast enough. Because when I let it boot normal it won&#8217;t recognize the DVD but when I go to the RAID BIOS and out again then it will find it. Going to the normal BIOS doesn&#8217;t really help because if you leave that it will just reboot (which I think despins the DVD player). And I have no idea what caused Vista to refuse to install on the SSD the first few times and I have even less of an idea how I solved it it just did. 

Installing the software was a bit of a bigger challenge than installing the hardware. But that&#8217;s another story.

Ok I&#8217;ll shut up now.

 [1]: /index.php/All/?p=284
 [2]: http://www.mpl.be/
 [3]: http://www.coolermaster.com/products/product.php?language=nl&act=detail&tbcate=558&id=2589
 [4]: http://www.coolermaster.com/uploads/product/products_highlight/file1183726826179.jpg "Coolermaster Cosmos 1000"
 [5]: http://www.asus.com/products.aspx?l1=3&l2=179&l3=815&l4=0&model=2609&modelmenu=1
 [6]: http://images.tweaktown.com/imagebank/news_pr-asus-P6TWS-Professional-01a_full.png "Asus W6T pro Workstation"
 [7]: http://www.corsair.com/_datasheets/TR3X6G1333C9.pdf
 [8]: http://www.intel.com/products/processor/corei7/index.htm
 [9]: http://www.asus.com/products.aspx?modelmenu=1&model=2423&l1=2&l2=6&l3=656&l4=0
 [10]: http://www.asus.com/999/images/products/2423/2423_l.jpg "9800 GT"
 [11]: http://www.ocztechnology.com/products/power_management/ocz_gamexstream_power_supply-nvidia_sli_ready_
 [12]: http://www.ocztechnology.com/images/products/accessories/b/GXS_nvidia2.jpg "OCZ Power supply"
 [13]: http://www.ocztechnology.com/products/flash_drives/ocz_core_series_v2_sata_ii_2_5-ssd