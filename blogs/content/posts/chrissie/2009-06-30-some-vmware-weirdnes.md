---
title: Some VMware weirdness
author: Christiaan Baes (chrissie1)
type: post
date: 2009-06-30T08:47:24+00:00
ID: 487
url: /index.php/sysadmins/os/some-vmware-weirdnes/
views:
  - 2867
categories:
  - Operating Systems
tags:
  - vmware

---
Today I had a major computer crash, or something that looked like it. The computer had a bad spell and I was the victim. Don&#8217;t ask what went wrong because I have no idea. After rebooting a couple of times it came back as if nothing ever happened.

But something did happen. I have VMware workstation 6.5.1 on my machine with 4 open VM&#8217;s on it, 3 of them XP and one server 2003. It also had one closed VM with Win 7 on it. 

When everything decided to come back I started VMware workstation and tried starting the VM&#8217;s. The closed one had no problem (duh).

The next one was the win 2003 server. This one had major problems getting back online. It restarted a couple of times while doing some fancy stuff (way too fast for me to read) but after a couple of minutes all was fine. COOL.

Next was a VM I use without a network connection to test a little VB6 app I got last week. And that too started back up without a problem.

Then number 3 came along and that one was no longer to be found by VMware. It couldn&#8217;t find the vmx anymore, which was kinda strange since it was still there. And when I told it where it was it couldn&#8217;t open the file because of an error in the vmx file. Now vmx files are just plain textfiles so I opened it up and saw a big mess. The text was still there but it seemed like it belonged to another VM, so I did the the crazy thing and just copy pasted a good vmx file and changed the settings to match the old file more or less. And that fixed that machine.

The last one did the same thing as the server. It rebooted, ran chkdsk, rebooted again and again and again and again and again and again and again&#8230; you get the point. So I had to find something to repair it. The simplest thing is to reinstall xp and get on with life. Which is a little less simple than what one might think.
  
First I had to start the machine in bios mode.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwarepowerontobios.png" alt="" title="" width="569" height="281" />
</div>

you can than change the boot sequence so that the CD-ROM drive is first.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/vmwarebios.png" alt="" title="" width="640" height="475" />
</div>

Now pop in the CD and save and exit the bios. The machine will now reboot.

HOLD: But before you continue you have to make a virtual floppy drive
  
In my case I added on with the drivers of the VMwarescsi controller thing. It&#8217;s called vmscsi-1.2.0.4.flp. I guess you know how to do that.

When XP starts installing you press F6 when asked and when asked you press S to give it the drivers on the floppy. It will then continue to install. Without these drivers you will get an error saying you don&#8217;t have any hard disks, of course it is completely correct in saying that. 

What a fun day indeed.