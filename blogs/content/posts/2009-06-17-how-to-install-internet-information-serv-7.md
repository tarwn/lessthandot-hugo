---
title: How to Install Internet Information Services (IIS) On Windows 7
author: SQLDenis
type: post
date: 2009-06-18T00:22:00+00:00
ID: 476
excerpt: 'If you are upgrading from windows xp to windows 7 there is a change in the way you install Internet Information Services. On windows xp you would install IIS from add/remove windows components. This is different in Windows 7; it is very similar how it i&hellip;'
url: /index.php/sysadmins/os/how-to-install-internet-information-serv-7/
views:
  - 7243
rp4wp_auto_linked:
  - 1
categories:
  - Operating Systems
tags:
  - iis
  - windows 7

---
If you are upgrading from windows xp to windows 7 there is a change in the way you install Internet Information Services. On windows xp you would install IIS from add/remove windows components. This is different in Windows 7; it is very similar how it is done on Vista.

Go to Control Panel and click on Programs, on the following screen click on Turn Windows features on or off (see image below)

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/IIS1.PNG?mtime=1357140507"><img alt="" src="/wp-content/uploads/blogs/All/IIS1.PNG?mtime=1357140507" width="663" height="120" /></a>
</div>

After that, select Internet Information Services and click ok

<div class="image_block">
  <a href="/wp-content/uploads/blogs/All/IIS2.PNG?mtime=1357140588"><img alt="" src="/wp-content/uploads/blogs/All/IIS2.PNG?mtime=1357140588" width="403" height="190" /></a>
</div>

You will be presented with a progress box and when that is done you will probably need to restart you machine.

After you restarted, type http://localhost/ in the address bar of a browser and if IIS was started successfully you should see the message: Internet Information Services is running