---
title: How to make your VMware VM crash
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-06T06:40:19+00:00
ID: 411
url: /index.php/desktopdev/generalpurposelanguages/how-to-make-your-vmware-vm-crash/
views:
  - 2257
categories:
  - General Purpose Languages

---
Time for a short post. A couple of weeks ago I made A windows server 2003 VM for VMWare on my VMWare Workstation 6.5. Nothing very difficult, jst download the image from MSDN mount the image in something lik Virutal clonedrive and start installing. Everything worked just fine, installing was a breeze. 

But it kept crashing on me. SOmetimes it would run for a day sometimes for a few hours other times for a couple of days. Very weird. Nothing in the log files, nothing on goolge that made sense. And it crashed really hard too. Reboot kinda of hard. No I won&#8217;t let you kill the process kinda of hard. And the symptom is a black VMWare tab/screen, all the other tabs work just fine, the OS keeps working too. But no more reaction of it. 

What was the problem? Well it had choossen the virtual CD-drive as it&#8217;s virtual dvd-drive, does that make sense? I changed it to a physical drive and everything has been running crash free now for a few weeks.

Woohoo :D.