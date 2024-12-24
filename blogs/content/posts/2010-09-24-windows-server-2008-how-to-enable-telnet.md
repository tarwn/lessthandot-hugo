---
title: Windows Server 2008 – How To Enable Telnet
author: ca8msm
type: post
date: 2010-09-24T08:08:21+00:00
ID: 906
excerpt: |
  By default, Telnet is disabled on a Windows Server 2008 installation and if you try to run it you will see an error something along the lines of:
  
  
  
  So, here's a very simple walk-through of the steps I had to follow in order to enable Telnet on a Wi&hellip;
url: /index.php/sysadmins/os/windows/2008server/windows-server-2008-how-to-enable-telnet/
views:
  - 6774
rp4wp_auto_linked:
  - 1
categories:
  - 2008 Server

---
By default, Telnet is disabled on a Windows Server 2008 installation and if you try to run it you will see an error something along the lines of:

<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/telnet/telnet_notinstalled.jpg" alt="" title="" width="668" height="331" />

So, here's a very simple walk-through of the steps I had to follow in order to enable Telnet on a Windows Server 2008 box:

1. Open the Server Manager:

<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/telnet/telnet_servermanager.jpg" alt="" title="" width="453" height="528" />

2. Select "Features" from the left TreeView pane and then click "Add Features" from the right-hand pane:

<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/telnet/telnet_features.jpg" alt="" title="" width="716" height="379" />

3. Select "Telnet Server" from the list of available features and click Next followed by Install:

<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/telnet/telnet_server.jpg" alt="" title="" width="780" height="587" />

4. Wait for what seems ages (it was probably only 5 minutes but it seemed like a long time!) whilst it installs the server:

<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/telnet/telnet_installing.jpg" alt="" title="" width="777" height="583" />

5. When it is complete, open a command prompt, type "telnet" and you should be presented with the following screen:

<img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/SysAdmins/telnet/telnet_installed.jpg" alt="" title="" width="668" height="331" />