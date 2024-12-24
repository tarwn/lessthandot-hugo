---
title: Enabling 64-bit VM support on a HP Probook
author: Axel Achten (axel8s)
type: post
date: 2012-04-05T09:26:00+00:00
ID: 1586
excerpt: "When you have a 64-bit machine with a 64-bit OS, you just want you're Virtual Machines to run in 64-bit mode. I use VMWare Player to run my VM's but when I started the install of a 64-bit OS on the out-of-the-box configuration of my HP Probook I got thi&hellip;"
url: /index.php/sysadmins/hardware/enabling-64-bit-vm-support/
views:
  - 13216
rp4wp_auto_linked:
  - 1
categories:
  - Hardware
tags:
  - 64 bit
  - bios
  - data execution prevention
  - hp
  - probook
  - trusted execution
  - virtualization technology
  - vm
  - vmware player

---
When you have a 64-bit machine with a 64-bit OS, you just want you're Virtual Machines to run in 64-bit mode. I use VMWare Player to run my VM's but when I started the install of a 64-bit OS on the out-of-the-box configuration of my HP Probook I got this error message:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios1.png?mtime=1333624722"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios1.png?mtime=1333624722" width="432" height="201" /></a>
</div>

The error message itself is clear, but where do you find the settings on you're machine? Googling/binging will return lots of hits with posts from people asking the same question. So I increased my geek factor by taking some pictures of my BIOS settings to make this post. To get started boot up you're machine and start hitting the ESC key untill you reach this menu:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios2.png?mtime=1333624733"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios2.png?mtime=1333624733" width="206" height="301" /></a>
</div>

Now hit you're F10 key so you get in the BIOS Setup screen en click on the System Configuration tab:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios3.png?mtime=1333624755"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios3.png?mtime=1333624755" width="500" height="201" /></a>
</div>

Next click on the Device Configuration link:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios4.png?mtime=1333624767"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios4.png?mtime=1333624767" width="305" height="282" /></a>
</div>

Finally we arrive in the menu we need, first check the Virtualization Technology check box so you're hardware supports 64-bit virtualization:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios5.png?mtime=1333624778"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios5.png?mtime=1333624778" width="501" height="201" /></a>
</div>

But don't stop here, there is one more option to change, Data Execution needs to be enabled. Normally you would like to prevent execution of code from a non-executable memory region but not deselecting the Data Execution Prevention checkbox will result in the same error. So scroll up to the DEP checkbox and uncheck it:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios6.png?mtime=1333624823"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/64Bios6.png?mtime=1333624823" width="500" height="272" /></a>
</div>

Now you can exit the BIOS, make sure you save the changes and start having fun installing a 64-bit Virtual Machine.