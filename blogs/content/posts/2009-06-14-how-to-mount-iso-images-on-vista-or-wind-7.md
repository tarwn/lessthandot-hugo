---
title: How to mount ISO images on Vista or Windows 7
author: SQLDenis
type: post
date: 2009-06-14T11:57:17+00:00
ID: 470
url: /index.php/sysadmins/os/how-to-mount-iso-images-on-vista-or-wind-7/
views:
  - 16213
rp4wp_auto_linked:
  - 1
categories:
  - Operating Systems
tags:
  - iso files
  - mounting files
  - vista
  - windows 7

---
Every time you buy a new computer you of course need to install a bunch of software. In my case this would be stuff mostly from MSDN 

_Visual Studio 2008 + Service pack 1
  
SQL Server 2008 + Service pack 1
  
Expression Studio 2
  
Win XP SP 3 (to run on a virtual machine)
  
Windows 7 RC (to run on a virtual machine)
  
Visual Studio 2010 (to run inside the Win 7 virtual machine)
  
Office 2007 Ultimate_

The nice thing about Virtual machines is that you can mount an ISO right inside the machine, this way you don&#8217;t have to burn a CD or install a ISO mounter inside the virtual machine itself

If you upgraded from Windows XP to Vista you probably wonder how to mount images. On XP you might have used alcohol or the VCdControlTool provided (but unsupported) by Microsoft. On Vista (and I am running the 64 bit version) you can use [MagicISO][1]. If you end up using this tool on a regular basis the I would suggest paying the $29 for it otherwise companies will stop making tools like this.
  
MagicISO is very easy to use. After you install the program you will see a new icon in the taskbar.

To mount an ISO do the following
  
In the taskbar right click on the MagicISO button
  
![In the taskbar right click on the MagicISO button][2]

Select the topmost entry labeled Virtual CD/DVD-ROM, click on K: No Media
  
![Select the topmost entry labeled Virtual CD/DVD-ROM, click on K: No Media][3]

After that select Mount
  
![After that select Mount][4]

Now navigate to the folder that you have the ISO in
  
![navigate to the folder that you have the ISO in][5]

After you select the ISO you will see the following dialog box
  
![dialog box][6]

Now you can either run the program immediately or click on the K drive. It is as simple as that. MagicISO has versions optimized for Win 7 and Server 2008 and versions optimized for Vista

 [1]: http://www.magiciso.com/
 [2]: http://imgur.com/xVFHL.jpg
 [3]: http://imgur.com/tnvGo.jpg
 [4]: http://imgur.com/i47jQ.jpg
 [5]: http://imgur.com/4ae1m.jpg
 [6]: http://imgur.com/uHGpj.jpg