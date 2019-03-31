---
title: Don’t fall for the social engineering scam that makes you go to ammyy.com
author: SQLDenis
type: post
date: 2011-09-18T12:10:00+00:00
excerpt: |
  I am pretty sure that none of our readers would fall for this social engineering scam that would make you share your computer with someone else, I am writing about it because I know of one person who had a call like this but she luckily hung up.
  
  I he&hellip;
url: /index.php/sysadmins/security/don-t-fall-for-the/
views:
  - 15207
rp4wp_auto_linked:
  - 1
categories:
  - 2003 Server
  - Security
  - Windows
  - Windows 7
  - XP
tags:
  - scam
  - security
  - social engineering

---
I am pretty sure that none of our readers would fall for this social engineering scam that would make you share your computer with someone else, I am writing about it because I know of one person who had a call like this but she luckily hung up.

I heard about this on Twit&#8217;s [Security Now][1]( With Steve Gibson and Leo Laporte ). Here is how it works, you will get a call from someone based in India working for Microsoft telling you that your computer is infected. The first thing they do is tell you to go to start/run box, type _eventvwr_ and it will bring up Windows Event Viewer. Now they have you go to the aplication folder and look at all the errors that are there, this is because the computer is infected. 

Of course everyone that has been using windows for a while will know that it will always have errors.

The next part is pretty funny&#8230;.

They will tell you to go back to the Run box and type &#8220;inf&#8221;, then they will tell you that inf stands for **infected files**. They make you go to C:Windowsinf After you are there they will ask you if you can you see a lot of .inf and .pnf files in the INF directory. If you say yes, and you will, they will tell you that those are corrupted files. Next they will tell you to double-click one of the PNF files. Once you double-click it, you will see an error message like the one below

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/pnffile.PNG?mtime=1326496203"><img alt="" src="/wp-content/uploads/blogs/All/pnffile.PNG?mtime=1326496203" width="412" height="252" /></a>
</div>

This, they will tell you is proof that your machine is infected.

After that they will tell you that to fix it you need to do the following. Go to the Run box and type www.ammyy.com. Ammyy Admin &#8211; is a popular config-zero free remote desktop sharing software. It&#8217;s used for system administration, webinars and instant remote desktop access. So next they will take over your system by using this tool

Here is a also a video that someone recorded [video:youtube:cueN1-2lANA] 

So if you or any of the people you know get any of these calls, just tell them to hung up.

**Update** Microsoft dumps partner over support call scam, see here: http://www.pcpro.co.uk/news/370054/microsoft-dumps-partner-over-support-call-scam

 [1]: http://twit.tv/sn