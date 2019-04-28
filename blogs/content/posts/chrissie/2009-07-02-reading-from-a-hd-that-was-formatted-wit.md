---
title: Reading from a HD that was formatted with ext3 from Windows Vista
author: Christiaan Baes (chrissie1)
type: post
date: 2009-07-02T11:39:42+00:00
ID: 493
url: /index.php/sysadmins/os/reading-from-a-hd-that-was-formatted-wit/
views:
  - 5306
categories:
  - Operating Systems
tags:
  - ext3
  - read
  - windows

---
Yesterday, my nas died on me, actually it didn&#8217;t, I just couldn&#8217;t connect to it any more and this thing didn&#8217;t have a USB port only ethernet. 

So I decided to get the disk out and mount it in my desktop. Since it was Sata that was easy. But I hit a little snag and that is that it was formatted in ext3 and windows does not read ext3

So I googled and found [Linux reader 1.1 from diskinternals][1] you find it in the freeware section. Yes freeware. It&#8217;s very small and it installs in seconds.

You then get a screen with your drives on it, ext 3 drives included.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/diskinternals2.png" alt="" title="" width="733" height="671" />
</div>

You then choose the folder you want to recover and click on recover this folder in the sidebar.
  
After which it starts.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/diskinternals1.png" alt="" title="" width="693" height="554" />
</div>

And now I have one extra 250GB drive in my desktop.

And like Ted says, Backups are for sissies.

 [1]: http://www.diskinternals.com/download.shtml