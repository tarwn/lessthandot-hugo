---
title: Converting a VMWare 7 VM to run on ESXi
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-16T15:36:50+00:00
ID: 761
excerpt: |
  So today I wanted to transfer one of my VM's from my local machine running under VMWare Workstation 7 to a brandnew ESXi 4 server we have laying around.
  
  First I thought I would just copy the data to the datastore and open the VM from there but that g&hellip;
url: /index.php/sysadmins/os/converting-a-vmware-7-vm-to-run-on-esxi/
views:
  - 11846
categories:
  - Operating Systems
tags:
  - esxi 4
  - vmware
  - workstation 7

---
So today I wanted to transfer one of my VM&#8217;s from my local machine running under VMWare Workstation 7 to a brandnew ESXi 4 server we have laying around.

First I thought I would just copy the data to the datastore and open the VM from there but that gives you this error.

> &#8220;Failed to open disk scsi0:0: Unsupported and/or invalid disk type 7. Did you forget to import the disk first?Unable to create virtual SCSI device for scsi0:0, &#8216;/vmfs/volumes/\*\\*\*randomID\*\*/blahblah.vmdk&#8217; Module DevicePowerOn power on failed.

So then I looked a little further an read something about how to do this. And I found out that you have to use the VMWare vCenter Converter Standalone. Very easy to do, So here are the screenshots.

First we give it the source, this is the Workstation 7 vmx file we want to convert/transfer to ESXi.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/VMWareConvert1.png" alt="" title="" width="816" height="638" />
</div>

After clicking next we choose VMWare Infrastructure virtual machine from destination type. And insert our serveraddress, username and password.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/VMWareConvert2.png" alt="" title="" width="816" height="638" />
</div>

After clicking next again. We tell it which Datastore to use and what name to use. We can even change the version (4 or 7) 7 is for ESXi 4 only and 4 is when you want backward compatibility with ESXi 3.5. Yes that is a bit confusing.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/VMWareConvert3.png" alt="" title="" width="816" height="638" />
</div>

Clicking next gets us to this page. Where I changed nothing. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/VMWareConvert4.png" alt="" title="" width="816" height="638" />
</div>

The last page is a summary and you should now click Finish to continue and start the process.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/VMWareConvert5.png" alt="" title="" width="816" height="638" />
</div>

The last image shows you how long it will take and a nice processbar.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwareconverter/VMWareConvert6.png" alt="" title="" width="787" height="518" />
</div>

The disk was around 11GB and it took nearly 3 hours to complete it&#8217;s mission.