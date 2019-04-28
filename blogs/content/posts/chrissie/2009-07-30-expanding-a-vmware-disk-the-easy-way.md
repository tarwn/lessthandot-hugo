---
title: Expanding a VMWare disk the easy way
author: Christiaan Baes (chrissie1)
type: post
date: 2009-07-30T10:04:33+00:00
ID: 526
url: /index.php/sysadmins/os/expanding-a-vmware-disk-the-easy-way/
views:
  - 8769
categories:
  - Operating Systems
tags:
  - expand disk
  - vmware
  - workstation 6.5

---
Today I wanted to upgrade an existing VM from windows 2003 server to windows 2008 server. The machine has 10GB virtual disk but windows 2008 was not too pleased with that, it needed more. It needed 9606MB free space on the disk to complete the order. I knew I had been looking to do this before and I knew it wasn&#8217;t easy. But then I found a reference to the [VMWare vCenter Converter][1]. And how you can use that to make it much easier.

> **Disclaimer: this method doesn&#8217;t really expand the disk size it just creates a copy with a bigger disk. Subtle difference.**

So according to VMWare this is what the converter does. 

> Convert Physical Machines to Virtual Machines in Minutes
> 
> VMware vCenter Converter can run on a wide variety of hardware and supports most commonly used versions of the Microsoft Windows and Linux* operating systems. With this robust, enterprise class migration tool you can:
> 
> * Quickly and reliably convert local and remote physical machines into virtual machines without any disruption or downtime.
      
> * Complete multiple conversions simultaneously with a centralized management console and an intuitive conversion wizard.
      
> * Convert other virtual machine formats such as Microsoft Virtual PC and Microsoft Virtual Server or backup images of physical machines such as Symantec Backup Exec System Recovery or Norton Ghost to VMware virtual machines.
      
> * Restore VMware Consolidated Backup (VCB) images of virtual machines to running virtual machines.
      
> * Clone and backup physical machines to virtual machines as part of your disaster recovery plan.

And this is how I used it to expand/enlarge the size of the virtual disk of my VM.

I am going to do this for a workstation 6.5 VM.

First step, of course, is to download and install the standalone version of the program.

Then you start it up and give it the source file.

like this

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/vmwareconverter1.png" alt="" title="" width="816" height="636" />
</div>

The you give it the destination like this.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/vmwareconverter2.png" alt="" title="" width="816" height="636" />
</div>

and step 3 is the important bit. Got Data to copy and click Edit then change Data copy type to &#8220;Select volumes to copy&#8221; and now you can change the target size to whatever you like. Simple huh.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/vmwareconverter3.png" alt="" title="" width="816" height="636" />
</div>

This is the converter in action. It took about 5 minutes to convert.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/vmwareconverter4.png" alt="" title="" width="638" height="412" />
</div>

and see the result a 25Gig disk instead of a 10Gig disk.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/Converterresult.JPG" alt="" title="" width="368" height="514" />
</div>

Did I mention that this converter is free!!

One thing I noticed is that the converter created a new network driver so the network settings are no longer the same as the previous ones. I had to simply set the parameters again.

 [1]: http://www.vmware.com/products/converter/