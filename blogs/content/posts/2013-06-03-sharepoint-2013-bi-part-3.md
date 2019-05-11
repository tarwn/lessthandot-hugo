---
title: Building a SharePoint 2013 BI Demo Environment Part 3 – Installing the OS
author: Koen Verbeeck
type: post
date: 2013-06-03T12:17:00+00:00
ID: 2090
excerpt: |
  This part of the series will focus on installing the Windows Server 2012 OS on the virtual machine. In most cases, you have an image file with the installation media available. In the VM settings, go to the DVD drive and attach the image.
   
  Now start&hellip;
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-3/
views:
  - 28088
rp4wp_auto_linked:
  - 1
categories:
  - Business Intelligence
  - Excel Reporting
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
  - SSAS
  - SSRS
tags:
  - business intelligence
  - demo environment
  - hyper-v
  - sharepoint 2013
  - sql server
  - syndicated
  - virtual machine
  - windows server 2012

---
This part of the series will focus on installing the Windows Server 2012 OS on the virtual machine. In most cases, you have an image file with the installation media available. In the VM settings, go to the DVD drive and attach the image.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_01.png?mtime=1369927220" alt="" width="441" height="416" />][1]

Now start the VM.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_02.png?mtime=1369927220" alt="" width="308" height="246" />][2]

While the VM is starting, connect to it using the Hyper-V manager.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_03.png?mtime=1369927220" alt="" width="308" height="284" />][3]

A window resembling a remote desktop connection opens (remark however that this window is not the same as a remote desktop connection). The first screen will ask you language, keyboard input and time/currency format. Choose what is applicable for your situation and click _Next_.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_04.png?mtime=1369927220" alt="" width="431" height="319" />][4]

Choose _Install Now_. (I guess most people will get this right without me telling them to click it J)

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_05.png?mtime=1369927220" alt="" width="431" height="318" />][5]

Enter the product key of your installation and click _Next_.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_06.png?mtime=1369927220" alt="" width="381" height="286" />][6]

Choose the type of server installation. We'll make life easy and choose for the GUI version.  If you want to do a hardcore installation with only a command line, go for the core version. Click _Next_.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_07.png?mtime=1369927220" alt="" width="379" height="283" />][7]

Read the license terms (ha-ha) and select the "_I accept the license terms"_ checkbox. Click _Next_.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_08.png?mtime=1369927220" alt="" width="379" height="285" />][8]

In the next screen, choose the custom install option.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_09.png?mtime=1369927220" alt="" width="379" height="285" />][9]

Choose our VHD drive as the hard drive where you want to install Windows (there should normally be only one option) and click _Next_.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_10.png?mtime=1369927220" alt="" width="381" height="285" />][10]

Windows will start installing. Go grab a coffee.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_11.png?mtime=1369927220" alt="" width="378" height="285" />][11]

After the installation is finished, the server will reboot and you will be greeted with a prompt asking you for an administrator password.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_12.png?mtime=1369927220" alt="" width="506" height="203" />][12]

The login page appears. Login and behold the beauty of Windows Server 2012.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_13.png?mtime=1369927220" alt="" width="455" height="199" />][13]

Next we'll give some components friendlier names. Start up the PowerShell command window as an administrator (right-click on the icon in the start menu and select _Run As Administrator_ at the bottom). Execute the following commands:

_Rename-NetAdapter -Name "Ethernet" -NewName "EXTLAN"   
Rename-NetAdapter -Name "Ethernet 2" -NewName "INTLAN"   
Rename-Computer -NewName **SP2013BI** -Restart –Force_

The two statements will rename the network adapters. The external and internal LAN might be in another order, depending on how you added them to the VM. The third statement will rename the server to SP2013BI and restart the server.

**Optional configurations**

The following actions are optional, but I prefer to do them as they simplify working with the server.

  1. Screen resolution
By default the screen resolution will be lower than your video card can handle. Crank it up to the maximum and you can connect to your VM in full screen.

  2. Turn off Internet Explorer Enhanced Security Configuration
As we will use a browser to connect to our SharePoint server, it is preferable to get that annoying IE ESC screen out of the way. Go to local server in the server manager and disable it.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_15.png?mtime=1369927221" alt="" width="295" height="317" />][14]

  3. Turn off the firewall
The intent of this environment is a single-user demo environment, so a firewall is not needed. It will be easier to connect to your virtual machine from remote tools, such as SQL Server Management Studio. You can also turn this off in the server manager.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_16.png?mtime=1369927221" alt="" width="372" height="212" />][15]

  4. Activate Windows
Bit self-explanatory why you need to do this of course. You'll need a network connection in order to activate Windows.

  5. Enable Remote Desktop
Remote Desktop has some advantages over the Hyper-V management window, such as copy-paste in both directions. You can enable this feature from the server manager as well.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_17.png?mtime=1369927221" alt="" width="291" height="329" />][16]

  6. Turn off Shutdown Event Tracker
Unless you want to type a reason every time you restart the server, you'll want to disable this. This feature can be configured through the Group Policy editor. Go to Local Computer Policy > Computer Configuration > Administrative Templates > System. Double click _Disable Shutdown Event Tracker_ from the list and select the _Disable_ radio button. Click _OK_.

<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_18.png?mtime=1369927221" alt="" width="575" height="387" /></p> 

</a>

  * Install updates
If you have plenty of time and patience, you can use Windows Update to get your Windows Server up to date. Make sure to disable Windows Update after you're finished, because you don't want your server rebooting in the middle of a demo.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_19.png?mtime=1369927221" alt="" width="357" height="106" />][17]

  * Change VM boot order
This is totally optional, but it makes your VM boot a little bit faster. When the server is turned down, go to the VM settings and into the BIOS pane. Put IDE at the top of the list.

[<img src="/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_21.png?mtime=1369928210" alt="" width="441" height="416" />][18]</ol> 

This concludes this part of the series.

Go back to [overview][19].
  
Previous part: [Building the VM][20].
  
Next part: [Installing SQL Server][21].

 [1]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_01.png?mtime=1369927220
 [2]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_02.png?mtime=1369927220
 [3]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_03.png?mtime=1369927220
 [4]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_04.png?mtime=1369927220
 [5]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_05.png?mtime=1369927220
 [6]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_06.png?mtime=1369927220
 [7]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_07.png?mtime=1369927220
 [8]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_08.png?mtime=1369927220
 [9]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_09.png?mtime=1369927220
 [10]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_10.png?mtime=1369927220
 [11]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_11.png?mtime=1369927220
 [12]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_12.png?mtime=1369927220
 [13]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_13.png?mtime=1369927220
 [14]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_15.png?mtime=1369927221
 [15]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_16.png?mtime=1369927221
 [16]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_17.png?mtime=1369927221
 [17]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_19.png?mtime=1369927221
 [18]: /media/users/koenverbeeck/SP2013DemoEnv/Part3/InstallOS_21.png?mtime=1369928210
 [19]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-1
 [20]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-2
 [21]: /index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-4