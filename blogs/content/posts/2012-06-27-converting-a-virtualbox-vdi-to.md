---
title: Converting a Virtualbox VDI to a VMWare VMDK file
author: Axel Achten (axel8s)
type: post
date: 2012-06-27T11:46:00+00:00
ID: 1658
excerpt: 'Today I was "gifted" a Virtualbox VDI file with a setup I need to use to give some custom training. I tried Virtualbox once but it was not a success and also this time the machine failed to boot. It kept complaining about some video settings and the boo&hellip;'
url: /index.php/itprofessionals/other/converting-a-virtualbox-vdi-to/
views:
  - 6455
rp4wp_auto_linked:
  - 1
categories:
  - Other
tags:
  - convert
  - vdi
  - virtualbox
  - vmdk
  - vmware

---
Today I was "gifted" a Virtualbox VDI file with a setup I need to use to give some custom training. I tried Virtualbox once but it was not a success and also this time the machine failed to boot. It kept complaining about some video settings and the boot sequence was interrupted. Time to convert the VDI to a good old VMWare VMDK, but how? The first hit in Google showed me an easy way to do this on a Linux, the second showed a complex one with third party tools on Windows. So here's the easy one on Windows:
  
First of all you need to have [VirtualBox][1] installed. Now open a command prompt and use the following VboxManage syntax:

```cmd
%program filesOracleVirtualBoxVBoxManage clonehd 'sourcefile'.vdi 'destinationfile'.vmdk --format VMDK
```

The result looks like this:

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/VDI2VMDK.png?mtime=1340804694"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/axel8s/VDI2VMDK.png?mtime=1340804694" width="676" height="102" /></a>
</div>

Now I can create a new VMWare virtual machine, replace the blank VMDK with the converted VMDK from the VDI file and start working.
  
Sometimes it's good to see you can still rely on older technology.

 [1]: https://www.virtualbox.org/wiki/Downloads