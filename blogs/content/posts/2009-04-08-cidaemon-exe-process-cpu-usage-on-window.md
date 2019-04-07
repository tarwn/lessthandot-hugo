---
title: cidaemon.exe process CPU usage on Windows Server 2003 R2
author: Ted Krueger (onpnt)
type: post
date: 2009-04-08T09:42:55+00:00
ID: 378
url: /index.php/sysadmins/os/cidaemon-exe-process-cpu-usage-on-window/
views:
  - 20252
rp4wp_auto_linked:
  - 1
categories:
  - 2003 Server
  - Operating Systems

---
One of my Windows Server 2003 R2 boxes started showing CPU bumps of 0 to 10% late last night. In investigating this a process cidaemon.exe was found to be the process causing the small but consistent utilization.

This process is associated with full text indexing on Windows. Even if you don&#8217;t use full text indexing, Windows does. I found 3 catalogs setup on the server. One of the defaults being the system volume. Repeated attempts to configure full text indexing and fix the wild CPU usage all failed. The only way I was able to fix this problem was the delete the catalogs and recreate them. After that full text indexing went back to normal processing and cidaemon.exe was sent to the bottom of the processes list in task manager.

If you come across this fix it quickly. From my research this issue will build and CPU usage can raise up to 70% over my 10% average.

To find out what catalogs you have do the following. (you can do this on any Windows XP Pro and up OS)

Click Start&#8211;>Search
  
In the Right menu click &#8220;Change Preferences&#8221;
  
Then click &#8220;With Indexing Service (for faster local searches&#8221;
  
Then click &#8220;Change indexing service settings (advanced)

This opens the snap for indexing service. Write down you catalogs if you want to recreate them. Note: I found no valid reasoning to actually recreate these catalogs although I felt better putting the system back in the state it was.

First you have to stop them. Right click the catalog&#8211;>All tasks&#8211;>Stop
  
Then delete them and recreate if you see fit.

cidaemon.exe of course is ended when you stop indexing service. The test is when you recreate and start the catalogs. You should see full text indexing and the cidaemon.exe process acting as it should utilizing 1% CPU and no more along with no more memory than around 2400K