---
title: Enabling Hyper-V Platform in a Windows 8.1 VM
author: Brian Davis
type: post
date: 2013-12-22T03:23:00+00:00
excerpt: 'This weekend I decided to take advantage of the Windows Phone Preview Program for Developers and update my Lumia 920 to Update 3 for WP8.  While Update 3 was downloading and installing on my phone, I decided to install the Windows Phone SDK 8.0 which;'
url: /index.php/sysadmins/os/windows/enabling-hyper-v-platform-in/
views:
  - 12403
rp4wp_auto_linked:
  - 1
categories:
  - Windows
  - Windows 8

---
This weekend I decided to take advantage of the [Windows Phone Preview Program for Developers][1] and update my Lumia 920 to Update 3 for WP8. While Update 3 was downloading and installing on my phone, I decided to install the Windows Phone SDK 8.0 which includes Visual Studio Express for Windows Phone on a Win 8.1 VM that I use for development purposes.

Installation was quick and painless until the very end when I received a message stating that the installation was unable to enable Hyper-V. When I tried to enable it manually by going into Control Panel -> Programs & Features -> Turn Windows Features On or Off, the Hyper-V Platform checkbox was grayed out. Upon hovering over the check box I received a tool tip stating &#8220;Hyper-V cannot be installed: A Hypervisor is Already Running&#8221;.

At this point I was confused as I had previously installed VS Express for Windows Phone on another VM and had successfully run it along with the emulator (the emulator runs in Hyper-V). Because of this I knew the VM host machine was correctly setup to allow VM Workstation to run virtualized environments within VM&#8217;s. I then compared the two VM&#8217;s and found there were a few differences:

  * Original install was an older version of VS Express for Windows Phone
  * Original install was on a Windows 8 VM 
  * New install was the latest version of VS Express for Windows Phone 
  * New install was on a Windows 8.1 VM 
  * Both VM&#8217;s are running on VM Workstation 9.0.1 

After reviewing the two VM&#8217;s I realized there must be something different with either the latest version of VS Express for Windows Phone, Windows 8.1, or both. Doing a little more research I found there were two things I needed to do:

  1. Enable &#8220;Virtualize Intel VT-x/EPT or AMD-V/RVI&#8221; in the settings of the VM <div class="image_block">
      <a href="/media/users/brian78/HyperV/VMSettings.png?mtime=1387680976"><img src="/wp-content/uploads/users/brian78/HyperV/VMSettings.png?mtime=1387680976" alt="" width="362" height="140" /></a>
    </div>

  2. Open the .vmx file for the virtual machine in a text editor and add the following lines to the end of it 
      1. hypervisor.cpuid.v0 = &#8220;FALSE&#8221;
      2. mce.enable = &#8220;TRUE&#8221;

After doing both of these I started the VM and intended to go back in and turn on Hyper-V, however, when I got back to the settings I was happy to see that &#8220;Hyper-V Platform&#8221; was already enabled.

<div class="image_block">
  <a href="/media/users/brian78/HyperV/WinFeatures.png?mtime=1387680966"><img src="/wp-content/uploads/users/brian78/HyperV/WinFeatures.png?mtime=1387680966" alt="" width="415" height="364" /></a>
</div>

ï¿½

With Hyper-V successfully enabled and running I was then able to run VS Express for Windows Phone along with the Windows Phone Emulator.

By the time I was finished with this, Update 3 had successfully installed on my phone and I was ready to go. Now, not only do I not have to wait for AT&T to release updates, but I can start developing apps for my phone as well. Now I just need to find the spare time and start developing!

 [1]: http://dev.windowsphone.com/en-us/featured/update3