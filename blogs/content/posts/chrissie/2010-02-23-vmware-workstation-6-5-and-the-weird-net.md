---
title: VMWare Workstation 6.5 and the weird network problem.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-02-23T13:00:03+00:00
ID: 707
excerpt: "I kinda like my VMWare Workstation 6.5 (soon to be 7.0). I always have at least 3 VM's running. But I seemed to have some intermittent network problems. I use Bridged networking because that's what I need. Most of my VM's have a static ip so no problems&hellip;"
url: /index.php/sysadmins/networking/vmware-workstation-6-5-and-the-weird-net/
views:
  - 4965
categories:
  - Networking
  - Windows 7
tags:
  - bridged networking
  - vmware

---
I kinda like my VMWare Workstation 6.5 (soon to be 7.0). I always have at least 3 VM&#8217;s running. But I seemed to have some intermittent network problems. I use Bridged networking because that&#8217;s what I need. Most of my VM&#8217;s have a static ip so no problems there.
  
The problem usually happens after I force a close on a VM because sometimes they crash without asking me. And then a weird thing happens. The machine looses its outgoing connection. I can start the machine (albeit a bit slower than usual) and I can ping the machine. I can see the network connection is alive and well and I can ping my host machine. But nothing beyond that. The first couple of times this happened I googled and googled and tried a myriad of things to fix it but I never found a solution. The only solution was to reinstall the VM or use the backup. Not a very fun thing to do.
  
Anyway, I finally found the solution. Apparently when it crashes it might think that your bridged adapter is no longer any good and choose another one (no idea which or how to find out which (not an expert).

First of all, on an unrelated note, when using bridged networking you can disable the 2 VMNet adapters on your host. And while you are at it disable all the others you don&#8217;t use too. This should prevent it from choosing the wrong adapter to begin with.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmware/vmware1.png" alt="" title="" width="1039" height="169" />
</div>

Now to the solution I found worked best for me. In the edit menu of your Workstation click on Virtual network editor. And then go to the tab Host Virtual Network Mapping. Change it from &#8220;Bridged to an automatically chosen adapter&#8221;.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmware/vmware2.png" alt="" title="" width="554" height="448" />
</div>

To the adapter of your choice. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmware/vmware3.png" alt="" title="" width="554" height="448" />
</div>

And then I restarted the VM and everything was nice and cheery in chrissieland again.