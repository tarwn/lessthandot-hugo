---
title: Rule "Previous releases of Microsoft Visual Studio 2008" failed
author: ca8msm
type: post
date: 2009-04-09T09:58:03+00:00
ID: 379
url: /index.php/datamgmt/dbprogramming/rule-previous-releases-of-microsoft-visu/
views:
  - 21242
rp4wp_auto_linked:
  - 1
categories:
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
I recently tried to install SQL Server 2008 Express Edition, only to receive a nice error message:

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/sql2008.png" alt="" title="" width="635" height="478" />
</div>

I found this rather strange, as I already have VS2008 SP1 installed:

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/vs2008sp1.png" alt="" title="" width="640" height="488" />
</div>

I found a few posts where other people were having the same problem, but most fixes usually involved uninstalling Visual Studio first and I really didn't want to go through this. Other workarounds suggested it would work if you unticked SSMS, but this wasn't something I wanted either! 

Luckily, there is a command argument that allows you to skip certain rules, so I ran the following command from a command prompt and successfully bypassed the rule! 😀

_setup.exe /ACTION=install /SkipRules=VSShellInstalledRule_

Now, I'm not sure if this should of worked, or why it did, but from what I can tell everything is working as it should and I can use both VS and SSMS without any problems.

I hope this helps someone as this is quite a time consuming task especially when you have to repeat it due to errors!