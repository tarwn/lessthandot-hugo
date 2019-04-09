---
title: You need to have hardware virtualization enabled if you want to run Windows 8 in Virtual Box
author: SQLDenis
type: post
date: 2011-09-14T00:56:00+00:00
ID: 1315
excerpt: "If your processor does not support Virtualization Technology then you can't run Windows 8 in Virtual Box....you will get a black screen of death. In order to find out if your CPU supports hardware Virtualization Technology you can download the Intel Pro&hellip;"
url: /index.php/sysadmins/os/windows/2003server/you-need-to-have-hardware/
views:
  - 43474
rp4wp_auto_linked:
  - 1
categories:
  - 2003 Server
tags:
  - virtualization
  - windows 8

---
If your processor does not support Virtualization Technology then you can't run [Windows 8][1] in Virtual Box….you will get a black screen of death. In order to find out if your CPU supports hardware Virtualization Technology you can download the Intel Processor Identification Utility here http://www.intel.com/p/en_US/support/highlights/processors/toolspiu/

Run it and look if Virtualization Technology is enabled…see the bottom of this picture
  
[<img src="http://farm7.static.flickr.com/6195/6145566403_c707fd4f42.jpg" width="500" height="167" alt="Intel Processor Identification Utility" />][2]

if it is enabled then you need to turn it on in your BIOS, to see how to configure BIOS, go to this page http://www.microsoft.com/windows/virtual-pc/support/configure-bios.aspx

After BIOS has been configured, you need to do a reboot, shut down, wait about 10 seconds and then start up again.

Now you are ready to make the changes in Virtual Box
  
Click on settings, then system and then on the motherboard tab, click on **Enable IO APIC**
  
[<img src="http://farm7.static.flickr.com/6064/6145566429_ddb2552029.jpg" width="500" height="218" alt="Enable IO APIC" />][3]

On the Processor tab, click on **Enable PAE/NX**
  
[<img src="http://farm7.static.flickr.com/6067/6145566459_b3eeb5a675.jpg" width="500" height="139" alt="Enable PAE_NX" />][4]

Finally on the Acceleration tab, click on both **Enable VT-x/Amd-v** and **Enable Nesting Paging**
  
[<img src="http://farm7.static.flickr.com/6180/6146115850_f53b27c536.jpg" width="500" height="130" alt="EnableVT" />][5]

Now you are all set…have fun

[<img src="http://farm7.static.flickr.com/6188/6145467215_0efbb953d9.jpg" width="500" height="374" alt="windows 8 metro start screen" />][6]

 [1]: /index.php/DesktopDev/MSTech/MSAccess/AccessFormsReports/windows-8-developer-preview-with
 [2]: http://www.flickr.com/photos/denisgobo/6145566403/ "Intel Processor Identification Utility by Denis Gobo, on Flickr"
 [3]: http://www.flickr.com/photos/denisgobo/6145566429/ "Enable IO APIC by Denis Gobo, on Flickr"
 [4]: http://www.flickr.com/photos/denisgobo/6145566459/ "Enable PAE_NX by Denis Gobo, on Flickr"
 [5]: http://www.flickr.com/photos/denisgobo/6146115850/ "EnableVT by Denis Gobo, on Flickr"
 [6]: http://www.flickr.com/photos/denisgobo/6145467215/ "windows 8 metro start screen by Denis Gobo, on Flickr"