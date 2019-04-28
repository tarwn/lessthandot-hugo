---
title: Th QNap TS-419P Turbo NAS
author: Christiaan Baes (chrissie1)
type: post
date: 2010-02-03T07:59:19+00:00
ID: 692
excerpt: "Yesterday 2 QNap TS-419P Turbo NAS arrived at work for me to play with. The question of course is how did I choose a solution that will work for me? To be honest pure luck. To be honest you have too much choice and choosing the right thing isn't going t&hellip;"
url: /index.php/sysadmins/hardware/th-qnap-ts-419p-turbo-nas/
views:
  - 5908
categories:
  - Hardware
tags:
  - backup
  - nas
  - qnap

---
Yesterday 2 [QNap TS-419P Turbo NAS][1] arrived at work for me to play with. The question of course is how did I choose a solution that will work for me? To be honest pure luck. To be honest you have too much choice and choosing the right thing isn&#8217;t going to happen unless you trust to pure luck or try some things out and that takes time. So I trust to luck. And no it does not help to read reviews or the manufacturer&#8217;s website since most sites will not tell you the truth anyway. Neither will I BTW I just tell you what I think is important for me. But that&#8217;s something that is normal on all blogs since blogs are just an egotrip anyway.

These 2 will serve as backup for the fileserver and will also store the sqlserver backups. My current backup plan will therefore consist of the following.
   
2 QNap 419 Nas servers that are synchronized on a daily basis with the fileserver shares. I will use [Vice-versa pro][2] for this since it&#8217;s cheap and gets the job done much better than BackupExec. Yes this will mean I will have 2 identical backups (better save than sorry I always say. And then I will connect an external 2.5 inch 320GB disk to one of the Nasses (is that a word?) and use it as my weekly backup that will be moved to the bank (I have 4 of these little things). And I will have the Nasses run in RAID 5 mode.

I think I&#8217;m pretty safe for the moment. Needless to say everything is on UPS and if a fire breaks out or other natural disaster occurs I will protect the things with my life. 

Time to give me some disasters, I&#8217;m ready!!!!!

The server will be equipped with an extra NIC and I will place the QNaps in a separate subnet together with that NIC. 

To begin with I also bought 8 320GB Disk (any SATA2 will do) I don&#8217;t need that much space anyway. They install very easily of course there are lots of little places where one can hurt himself but a little blood won&#8217;t kill ye.

The install is easy. Just follow the yellow brick road and find the wizard. Or just RTFM. I didn&#8217;t and just used my instinct and it worked from the first go, yes it&#8217;s that easy. 

Now for some pictures. 

This is it.

![QNap 419P][3]

Cool huh.

And this is what you get when you run the administration panel in FF
  
3.6

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/qnap/qnap1.png" alt="" title="" width="691" height="348" />
</div>

Not great. The funny things is is that the installation wizard works in FF3.6 but not the administration panel. 

So on to IE8 we go.

And look how pretty this looks.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/qnap/qnap2.png" alt="" title="" width="779" height="589" />
</div>

And it&#8217;s not only pretty it also works amazingly quick. 

You have all the tools you would need at your disposal.

You can see your disks and their status.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/qnap/qnap3.png" alt="" title="" width="757" height="415" />
</div>

System information.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/qnap/qnap4.png" alt="" title="" width="757" height="415" />
</div>

Resource monitor.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/qnap/qnap5.png" alt="" title="" width="763" height="536" />
</div>

And much, much more. Geek/nerd heaven.

Go on do yourself a favor and buy one.

 [1]: http://www.qnap.com/pro_detail_feature.asp?p_id=127
 [2]: http://www.tgrmn.com/
 [3]: http://www.qnap.com/images/products/NAS/TS419P/feature02.jpg "QNap 419 P"