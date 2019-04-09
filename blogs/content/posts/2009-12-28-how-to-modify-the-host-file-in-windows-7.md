---
title: How To Modify The Host File In Windows 7 Or Vista
author: SQLDenis
type: post
date: 2009-12-29T01:02:58+00:00
ID: 656
excerpt: 'One way I stay protected from bad sites is that I use a modified host file. In Vista and Windows 7 you cannot just open the file in notepad and save it anymore, you will get an error. Someone had this happen at work on Windows Server 2008 last week and&hellip;'
url: /index.php/sysadmins/security/how-to-modify-the-host-file-in-windows-7/
views:
  - 12774
rp4wp_auto_linked:
  - 1
categories:
  - Security
  - Vista
  - Windows
  - Windows 7
tags:
  - host file
  - malware
  - protection
  - security
  - spyware

---
One way I stay protected from bad sites is that I use a modified host file. In Vista and Windows 7 you cannot just open the file in notepad and save it anymore, you will get an error. Someone had this happen at work on Windows Server 2008 last week and just today someone asked about it in our forums here: [Can't modify the host file on Windows 7][1] 

I decided to create a short little blog post explaining how you can modify this file.

If you open the host file in the following location C:WindowsSystem32driversetc with notepad and then you want to save it you will get one of these errors in Win 7/Vista/Server 2008

**Access to C:WindowsSystem32driversetchosts was denied**

**Cannot create the C:WindowsSystem32driversetchosts file.
  
Make sure that the path and file name are correct.**

I am getting the following error on my Windows 7 machine

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins/error.png" alt="" title="" width="395" height="184" />
</div>

In order to fix this you first need to run notepad as an adminstrator. Here is how you do this. Click Start&#8211;>All Programs&#8211;>Accessories, right-click on Notepad, and then click Run as administrator. This might ask you for a password, enter it

<div class="image_block">
  <img src="/wp-content/uploads/blogs/SysAdmins//RunAsAdmin.png" alt="" title="" width="270" height="162" />
</div>

Now from notepad press CTRL + O (or File&#8211;> Open) and navigate to the host file after you make your changes save it..that should work.

I like to use the MVPS HOSTS file, this file is pretty extensive, it has over 16000 entries. Since my PC is used by my wife and my oldest son this is some extra protection that I have in case they get to a bad site.

Here is just a small sample of entries in this host file

_\# [Rogue/Suspect Anti-Spyware Products]
  
127.0.0.1 6d-antivirus.com
  
127.0.0.1 www.6d-antivirus.com
  
127.0.0.1 www.ad-purge.com #[thespywareshield.com]
  
127.0.0.1 www.adware-remover.net #[ADS Adware Remover]
  
127.0.0.1 antiadwarepro.com
  
127.0.0.1 www.antiadwarepro.com
  
127.0.0.1 buylicensekey.com
  
127.0.0.1 www.codeclean.co.kr #[Adware-CodeClean]
  
127.0.0.1 www.doctorvaccine.co.kr #[McAfee.DoctorVaccine]
  
127.0.0.1 errordoctor.com
  
127.0.0.1 www.errordoctor.com #[Win32/Adware.ErrorDoctor]
  
127.0.0.1 www.esunsofttechnologies.com #[Symantec.MyBugFreePc]_

Here is the link to the file in plain text <http://www.mvps.org/winhelp2002/hosts.txt>

What about you, do you use the host file to block bad sites?

 [1]: http://forum.ltd.local/viewtopic.php?f=139&t=9275