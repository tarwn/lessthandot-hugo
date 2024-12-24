---
title: Building a SharePoint 2013 BI Demo Environment Part 2 – Building the VM
author: Koen Verbeeck
type: post
date: 2013-05-31T07:47:00+00:00
ID: 2089
excerpt: "In this blog post I'll explain how I build the VM and VHD for my virtual demo environment. I use Hyper-V locally on my Windows 8 OS to run my virtual environment. If you have Windows 8 Enterprise, Hyper-V is a feature you can simply enable in the contro&hellip;"
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/sharepoint-2013-bi-part-2/
views:
  - 17851
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

---
<p style="text-align: justify;">
  In this blog post I'll explain how I build the VM and VHD for my virtual demo environment. I use Hyper-V locally on my Windows 8 OS to run my virtual environment. If you have Windows 8 Enterprise, Hyper-V is a feature you can simply enable in the control panel.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_00.png?mtime=1369838970"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_00.png?mtime=1369838970" alt="" width="343" height="300" /></a>
</p>

<p style="text-align: justify;">
  <em>(If any of the screenshots is too small, click on the picture to view it in real-size format.)</em>
</p>

<span style="font-weight: bold; text-align: justify;">Create the virtual hard disk</span>

<p style="text-align: justify;">
  The first step is to create a virtual hard disk. I choose for the VHD format, as it is widely supported by other virtualization software as well, so it is easy to convert the virtual machine to VirtualBox, for example. In Hyper-V, right-click on your computer name, click <em>New</em> and choose <em>Hard Disk...</em>
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_01.png?mtime=1369838970"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_01.png?mtime=1369838970" alt="" width="361" height="265" /></a>
</p>

<span style="text-align: justify;">In the wizard, choose the VHD format and click </span>_Next_

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_02.png?mtime=1369838970"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_02.png?mtime=1369838970" alt="" width="430" height="324" /></a>
</p>

<span style="text-align: justify;">In order to save disk space, choose a </span>**dynamically expanding VHD**<span style="text-align: justify;">. In theory Fixed Size VHD are faster, but this means you lose all that disk space, something I'd rather avoid on my SSD. Furthermore, more recent versions of Hyper-V have closed the performance gap between these two types quite a bit, so there's no reason not to choose for the dynamic VHD.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_03.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_03.png?mtime=1369838971" alt="" width="430" height="324" /></a>
</p>

<span style="text-align: justify;">Specify a name and a location for your VHD and click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_04.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_04.png?mtime=1369838971" alt="" width="430" height="324" /></a>
</p>

<span style="text-align: justify;">I configured the size of the VHD to be 40GB maximum. This might seems small for a single server environment with SharePoint 2013 and SQL Server, but at the time of writing the size of my VHD is nearly 30GB. I expended the size later on, as the size of the VHD will grow with the continued use of the VM and I didn't want issues in the middle of a demo. </span><a style="text-align: justify;" href="http://www.petri.co.il/expanding-virtual-hard-disks-with-hyper-v.htm">This article</a> <span style="text-align: justify;">explains how you can expand a VHD. You don't need to follow my 40GB limit; choose a size you are comfortable with (and which your hard drive can support). If you choose big enough immediately, you don't need to expand the VHD later on.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_05.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_05.png?mtime=1369838971" alt="" width="430" height="324" /></a>
</p>

<span style="text-align: justify;">You'll get an overview of the actions to be taken. Click </span>_Finish_ <span style="text-align: justify;">to create the VHD.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_06.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_06.png?mtime=1369838971" alt="" width="430" height="324" /></a>
</p>

<span style="font-weight: bold; text-align: justify;">Create a network adapter</span>

<p style="text-align: justify;">
  Assuming you'll want to connect to your virtual machine with SQL Server Management Studio, a browser or with a remote desktop connection, you need a network adapter.
</p>

<p style="text-align: justify;">
  In the Hyper-V manager, go to the <em>Virtual Switch Manager</em>. Select Internal and click on <em>Create Virtual Switch</em>. An internal network adapter will allow you to communicate between your host machine and your virtual machine.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_10bis.png?mtime=1369839732"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_10bis.png?mtime=1369839732" alt="" width="360" height="140" /></a>
</p>

<span style="text-align: justify;">In the following screen, type a name for the virtual switch and click on </span>_OK_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_10.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_10.png?mtime=1369838971" alt="" width="441" height="416" /></a>
</p>

<span style="font-weight: bold; text-align: justify;">Create the virtual machine</span>

<p style="text-align: justify;">
  Next we'll create the virtual machine using the VHD created in the previous step. Right-click on the computer name in Hyper-V, choose <em>New</em> and this time select <em>Virtual Machine... </em>Specify a name and location for your virtual machine.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_07.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_07.png?mtime=1369838971" alt="" width="430" height="324" /></a>
</p>

<span style="text-align: justify;">Configure the amount of memory your VM will use. In my case 20GB of RAM. SharePoint 2013 needs quite some memory, so don't be cheap on this. You can always change the amount of memory at a later point in time,  but you'll have to turn down the machine to do so.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_08.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_08.png?mtime=1369838971" alt="" width="430" height="324" /></a>
</p>

<span style="text-align: justify;">Next you'll have to configure networking. Choose the virtual switch we created earlier and click on </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_09.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_09.png?mtime=1369838971" alt="" width="430" height="324" /></a>
</p>

<span style="text-align: justify;">In this step you choose the virtual hard disk. Select the option </span>_Use an existing virtual hard disk_<span style="text-align: justify;">, specify its location and click </span>_Next_<span style="text-align: justify;">.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_11.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_11.png?mtime=1369838971" alt="" width="430" height="324" /></a>
</p>

<span style="text-align: justify;">You'll get an overview of the actions to be taken. Click </span>_Finish_ <span style="text-align: justify;">to end the wizard.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_12.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_12.png?mtime=1369838971" alt="" width="430" height="324" /></a>
</p>

<span style="font-weight: bold; text-align: justify;">Additional configuration</span>

<p style="text-align: justify;">
  Power View can create dynamic geographic maps with just a few clicks. In order to do this, it needs a connection to Bing Maps. We'll add an external network adapter so the VM can connect to the Internet.
</p>

<p style="text-align: justify;">
  Follow the same steps we took to create the internal virtual switch, but choose <em>External</em> this time. Specify a name and the actual physical network adapter it can use from the dropdown and click on <em>OK</em>.
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_13.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_13.png?mtime=1369838971" alt="" width="437" height="412" /></a>
</p>

<span style="text-align: justify;">Right-click on your virtual machine and choose </span>_Settings._ <span style="text-align: justify;">In the Hardware section, go to </span>_Add Hardware_ <span style="text-align: justify;">and add a network adapter.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_14.png?mtime=1369838971"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_14.png?mtime=1369838971" alt="" width="441" height="416" /></a>
</p>

<span style="text-align: justify;">In the newly added adapter, choose the virtual switch you just created from the dropdown.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_15.png?mtime=1369838972"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_15.png?mtime=1369838972" alt="" width="441" height="416" /></a>
</p>

<span style="text-align: justify;">I also set the number of processors to a higher number. Go to </span>_Processor_ <span style="text-align: justify;">and change the number of processors the VM can use.</span>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_16.png?mtime=1369838972"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_16.png?mtime=1369838972" alt="" width="441" height="416" /></a>
</p>

<p style="text-align: justify;">
  <a href="/media/users/koenverbeeck/SP2013DemoEnv/Part2/createVMandVHD_16.png?mtime=1369838972"></a>You can opt to add additional hard drives. I only used one hard drive as it is a fairly simple demo environment with only 1 concurrent user. For performance reasons it would make sense to add more disks to your environment. Just create extra VHD disks and add them to the VM. Be aware that the IDE Controller only supports up to 3 drives of which one is already taken by the DVD drive. So if you want to add more than 1 additional hard drive, you need to add them to the SCSI controller. The good news is you don't need to shut down the VM to add drives to the SCSI.
</p>

<p style="text-align: justify;">
  Go back to <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-1">overview</a>.<br />Next part: <a href="/index.php/DataMgmt/business-intelligence-1/sharepoint-2013-bi-part-3">Installing the OS</a>.
</p>