---
title: Powershell, WmiObject and a corrupt repository
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-25T07:20:00+00:00
ID: 1054
excerpt: 'Yesterday I was dabling in powershell a bit and found that this line Get-WMIObject Win32_Process of code did not work. I got an invalid class exception. And sure enough when I did a Get-WMIObject -list I did not see Win32_process in that list. So someth&hellip;'
url: /index.php/desktopdev/generalpurposelanguages/powershell-wmiobject-and-a-corrupt/
views:
  - 5164
categories:
  - General Purpose Languages

---
Yesterday I was [dabling in powershell][1] a bit and found that this line <code class="codespan">Get-WMIObject Win32_Process</code> of code did not work. I got an invalid class exception. And sure enough when I did a <code class="codespan">Get-WMIObject -list</code> I did not see Win32_process in that list. So something was terribly wrong. And for once Google was not very helpfull either. Trying that piece of code on another machine did work, so the problem was local. I then started looking for problems with WMI and how to reinstall it and found [this website by Katy Coe][2]. Who sadly stopped blogging in 2008. The article in question is a [Tutorial: How To Fix WMI Corruption][3]. And man did it help. I did not run true the stages she suggested. I tried the last step first.

  * I stopped the WMI service
  * I renamed the repository folder
  * I restarted the service
  * I went to WMI control
  * I waited
  * I recompiled the repository like this
```
c:
cd windowssystem32wbem
for /f %s in ('dir /b *.mof *.mfl') do mofcomp %s```
  * I went back to WMI control and I saw that all was good.

So if life is treating you badly and you get a ManagementException with an Invalid class message while getting a WMIObject you can be pretty sure your repository is corrupt. Best thing to do is to repair it then.

Apparently you can get data loss with this method, but I don&#8217;t know I really care since the darn thing didn&#8217;t work anyway. 

So when you have WMI problems read the magnificent article Katy wrote and if that didn&#8217;t help you might need to reinstall windows.

 [1]: /index.php/DesktopDev/GeneralPurposeLanguages/my-first-steps-in-powershell
 [2]: http://www.djkaty.com/
 [3]: http://www.djkaty.com/wmicorruption