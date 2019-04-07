---
title: 'Building the Virtual Lab: VMWare and MS Windows 2008 R2'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-05-11T12:41:54+00:00
ID: 775
excerpt: 'Asking me what I do for living could net you different answers, depending on what day you ask. Over the years, and through the course of several jobs, I have had a wide range of responsibilities that often stretched well beyond my current organizational&hellip;'
url: /index.php/sysadmins/os/building-the-virtual-lab-vmware-and-2008/
views:
  - 22263
rp4wp_auto_linked:
  - 1
categories:
  - 2008 Server
  - Operating Systems
  - Windows
tags:
  - installation
  - virtual lab
  - vmware
  - windows server 2008 r2

---
Asking me what I do for living could net you different answers, depending on what day you ask. Over the years, and through the course of several jobs, I have had a wide range of responsibilities that often stretched well beyond my current organizational role. I like to call these opportunities.

Recently I was considering the idea of building a virtual lab, as I&#8217;ve been worried that some of the varied experiences or skills I&#8217;ve picked up would rust away. While in Wisconsin visiting Ted Krueger ([twitter][1] | [blog][2]) for [SQL Saturday Chicago][3], I realized that the lab could serve two purposes. I could use it to not only regain or sharpen aging skills, but I could also use it to start blogging on more technical matters. Don&#8217;t get me wrong, I love process and personal improvement topics, but I need my tech fix too.

So here we are. If you don&#8217;t already have a virtual lab at work or home then I invite you to follow along with me as I build one. Each blog entry will have a general level of difficulty, from basic installation  ![propeller beanie][4]to advanced administration ![wizard's hat][5]. As the lab grows we will be installing a wide number of systems then later configuring and tuning them to meet different circumstances and needs. 

Lets get started.

<div class="acc_header">
  <h2>
    Virtual Lab: Building the First Windows 2008 R2 Virtual Machine
  </h2>
  
  <p>
    The purpose of today&#8217;s article is to create a basic windows virtual machine that can serve as a template for later virtual machines. Having a basic image or set of images on hand can dramatically speed up production and non-production roll-outs as well as ensure standardization of systems across your environment.<br /> <br /> <label>Technical Area:</label> Accidental Systems Administrator<br /> <label class="diff">Level of Difficulty: </label><img src="http://tiernok.com/LTDBlog/dr_basic.png" alt="Basic Difficulty" /><br /> <label>Additional Articles:</label><a href="http://wiki.ltd.local/index.php/Virtual_Lab" title="View the wiki entry">Virtual Lab entry on the LTD Wiki</a>
  </p>
</div>

## Building the Lab Environment

Building your own lab can be both fun and a learning experience. A virtual lab provides you a safe place to try new technologies and configurations. When you create a really horrendous set of configurations in your lab, your halls are going to be remarkably free of end users with torches and pitchforks. The risks you should never take in production environments, even production development systems, are fair game here. But first we have to build it.

### Environment

First we should determine what type of environment we want to run and what types of technologies or systems we would like to play with. Some of you will be planning no working with just development software and/or database servers, some will be want to create a virtual business environment (DC, Exchange, AD, etc), and some will even want to play with a mixed operating system setup. Identifying some general requirements will also help when your lab (or the market) grows past the current list of technologies you want to try.

Here is a sample set of questions you should consider:

<ol style="margin-left: 2em;">
  <li>
    What OS&#8217;s are you intending to run? Desktop, Server, Windows, Unix, Linux ?
  </li>
  <li>
    What major software packages or technical areas are you intended to explore in the lab?
  </li>
  <li>
    Will any of the systems be used for active development (ie, results will be ported directly to production)?
  </li>
  <li>
    Will any of the systems potentially move to production?
  </li>
  <li>
    Will any of the systems need to travel with you (between sites, work and home, or to conferences)?
  </li>
  <li>
    Will your systems need to run only some of the time, all the time, or a mix?
  </li>
  <li>
    Will they require network access to the outside world?
  </li>
  <li>
    Will the outside world be allowed to access your lab resources?
  </li>
  <li>
    Will you need a private network in your lab or can they coexist on your existing network?
  </li>
  <li>
    Will there be interdependencies? (for instance, Server A is running DNS and Active Directory and has to be on for Server B to work properly)
  </li>
</ol>

<div class="mylab">
  And some of the answers for my lab:</p> 
  
  <ul>
    <li>
      My lab will be running multiple Windows servers and 1-2 linux systems
    </li>
    <li>
      Software packages: SQL Server (of course), TFS, Active Directory, Sharepoint, potentially a DC, possibly Exchange, &#8230;pretty much anything in Microsoft&#8217;s catalog or that comes in as a user request is fair game
    </li>
    <li>
      None of these systems will go to a production environment or be accessed by active development environments
    </li>
    <li>
      The only environments that may need to travel are the SQL Server ones
    </li>
    <li>
      None of the systems will need to run all of the time
    </li>
    <li>
      The lab systems will be on a private network with limited external access
    </li>
  </ul>
</div>

### Platform Selection

Once we have an idea what we our lab will be used for, we need to determine what type of platform to use. If this is a home lab or you have limited space for equipment, you will probably want to go with a virtualization platform. Using a virtualization platform allows us to have a smaller physical (and electrical) footprint and multi-purpose our equipment, since many of our test servers will not need to be running when we aren&#8217;t directly using them. Virtualization also allows us to move systems to upgraded hosts if we later find ourselves having performance problems.

The hardware you use will depend on the types of answers you provided to the questions above. Using an old desktop might be a acceptable solution or you may need to incorporate higher end systems or even spare servers. My last setup (2007-2009) used an off-the-shelf Dell Precision work station as the host without any extra equipment or over-the-top upgrades. The system performed well as a low budget SQL Server and Windows server platform. On the other hand my new environment will need to support a far wider range and number of systems, so it will require higher performance hardware.

<div class="mylab">
  Here is the current hardware setup for the lab:</p> 
  
  <ul>
    <li>
      CPU: A Intel i7 920 quad-core
    </li>
    <li>
      Memory: 10 GB of DDR3 RAM w/ room to upgrade up to 24GB
    </li>
    <li>
      Storage #1: Raid 10 Array &#8211; (4) SATA2 7200RPM 500GB drives, mixed manufacturers
    </li>
    <li>
      Storage #2: <a href="http://www.amazon.com/Vantec-NST-360SU-BK-3-5-Inch-External-Enclosure/dp/B000EDKO04/ref=sr_1_1?ie=UTF8&s=electronics&qid=1272804217&sr=8-1" title="View at Amazon">Vantec external enclosure</a> w/ 500GB SATA2 drive (USB2/eSata) &#8211; raw storage for some backups, MSDN images, and other purchased software
    </li>
    <li>
      Storage #3: <a href="http://www.amazon.com/TWO-BAY-SATA-DOCKING-STATION/dp/B001UHQFVA/ref=sr_1_5?ie=UTF8&s=electronics&qid=1272804276&sr=1-5" title="View at amazon">Bytecc two-bay docking station</a> (USB2/eSata) &#8211; more disk (will be critical in a later article)
    </li>
  </ul>
  
  <p>
    I have selected VMWare for my virtualization platform. There are a number of reasons for this (for instance, Microsoft hard-blocking virtual server installations on Windows 7) and I actually would have preferred MS Virtual Server, but such is life.
  </p>
</div>

## Installing our First Virtual Server

From this point forward we will be working from the comfort of my home lab, so examples will be based on VMWare Server 2.1 and I will point out versions for other software as it is introduced. The purpose of this first virtual machine is to serve as a template for later machines, allowing me to build out one image that with a standard base configuration, installs all common services, then apply all the outstanding Windows updates. Though I will be using the term &#8216;template&#8217; in the article, this shouldn&#8217;t be confused with the template capability in VMware&#8217;s vSphere. 

<div class="hint">
  In a production environment it is extremely helpful to have a basic server image already pre-configured and up to date. Not only does it provide a faster alternative to building each server from scratch, but it ensures that each server goes out with all of the required services and configurations and does so without adding a checklist to your process. Not that I am a <a href="/index.php/ITProfessionals/ITServiceManagement/there-is-never-time-for-part-3" title="Or maybe I am">process improvement fan</a> or anything.
</div>

Though the software is different, many of the concepts will be similar if your lab is going to be based on Microsoft Virtual Server 2005 and I have successfully followed almost the same exact steps through Virtual Server in the past.

### Build the Server

First we need to build our virtual machine. Opening the VMWare Server Host and logging in with administrative credentials, we are presented with a dashboard view of our system.

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/vmware_beginning.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/vmware_beginning.png" title="VMWare, Freshly Installed" /></a><br /> VMWare, Freshly Installed
</div>

Clicking the &#8220;Virtual Machine&#8221; menu provides an option to &#8220;Create Virtual Machine&#8221; and a wizard to walk us through the creation of our virtual machine.

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/vmware_create_vm.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/vmware_create_vm.png" title="VMWare Menu, Create VM" /></a><br /> Create a new VM
</div>

While it isn&#8217;t critical to pick a good name at this point, it couldn&#8217;t hurt either. Later as we create servers for specific purposes the VM names will reflect the actual names of their servers.

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/vm_step1.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/vm_step1.png" title="Creating a new VM, Step 1" /></a><br /> New VM &#8211; Provide a Name
</div>

In my case I intend to use Windows 2008 R2 as my main server environment, so this basic image will reflect that in it&#8217;s name and I will choose the Windows 2008 option from the operating systems tab. Before choosing the 64-bit image I made sure to run the [64-bit compatibility check][6] offered by VMWare to ensure I could run a 64-bit guest on my host. If you are using Virtual Server 2005 then you will be limited to 32-bit guests so be sue you buy/download the correct versions.

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/vm_step2.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/vm_step2.png" title="Creating a new VM, Step 2" /></a><br /> New VM &#8211; OS Selection
</div>

For this first server I am going to allocate 4GB of RAM for use during the installation process. When making later copies of the system we will tune these values accordingly. As I intend to use these systems in a lab environment and may need the images to be portable to another single core system, I&#8217;m going to select the 1 CPU option. For later articles we may create 2- or 4-CPU options, but for most of our non-production needs a 1-CPU system will be sufficient. While technically it would be possible to &#8220;tune&#8221; the CPU setting at a later time, since we really only care about the hard-drive file at this point, changing CPU architectures on a system after installation ranges from tricky to absurdly-painful-with-no-hope-of-working.

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/vm_step3.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/vm_step3.png" title="Creating a new VM, Step 2" /></a><br /> New VM &#8211; RAM and CPU Settings
</div>

The final lasting decision (though not the last step) is how large we want to make the virtual drive and whether we want to allocate the space ahead of time or use a growth model. In my last environment I pre-allocated space for my base windows installation and then attached or created secondary drives for the installed applications. In this case, Microsoft is suggesting [a minimum of 32GB of space][7] and, while I don&#8217;t want to give up the space, I&#8217;ve decided to use pre-allocation again to simplify the process.

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/vm_step4.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/vm_step4.png" title="Creating a new VM, Step 3" /></a><br /> New VM &#8211; Drive Settings
</div>

The final options, optical drive access and network method, are only going to be used for this image during the Windows installation process. Later copies will again have values assigned based on the project or technology that&#8217;s being installed. For the purposes of the installation we will use an ISO file for the optical drive and bridged networking.

<div class="hint">
  When you install VMWare initially it will ask for a folder for your standard store (default is &#8220;C:Virtual Machines&#8221; on Windows). ISOs placed in this folder will be available for mounting in VMWare or you can create a new store that points to a folder elsewhere. Having a dedicated drive for software storage, I created an &#8216;MSDN Library&#8217; store and pointed it to that location.
</div>

<div class="hint">
  VMWare Gotchas:<br /> Firefox 3.6 is documented as not working with the console viewer right now, so you are going to have to downgrade or switch to IE. 3-3.5 also had issue at one point but there are either fixes or workarounds for this.
</div>

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/vm_setup_done.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/vm_setup_done.png" title="Setup is Complete" /></a><br /> VM Setup Complete
</div>

### Install the OS

We are now ready to turn our machine on for the first time and install Windows. This part is actually fairly boring, which is yet another reason that we want to limit how many times we have to do it.

<div class="mylab">
  This is the point I ran into an issue with VMWare&#8217;s console. VMWare continued to present me with a black screen when I opened the console. I believe the change that fixed this issue was setting the DNS address on the vmnet1 network connection, but it could just as easily have been the firewall being turned off when VMWare started or the couple changes I made to IE&#8217;s trusted sites. An excellent, practical example of why you should always make one change at a time and write down what you changed when you are troubleshooting a systems problem.
</div>

The first decision the installer requires is selection between the various versions of Windows. Based on the options available, the trade-offs outlined in [the feature comparison][8], and the fact that I have already written down my MSDN Enterprise key, I will be installing Enterprise edition. I&#8217;m not actually sure how long the installation took, I wandered off and found something else to do for a while (Peanut butter jelly time, peanut b&#8230;oh). I&#8217;ve installed windows a few more times than your average developer, so my bet is on 39 minutes. 😉

<div class="hint">
  The default Windows password policy is probably going to annoy you if you haven&#8217;t run into it before. It will not tell you what the policy is when your password fails and the policy itself is well-defined. In a production environment one of our first steps would likely be to change it to meet our business needs or current setup.</p> 
  
  <p>
    Official policy information can be found <a href="http://technet.microsoft.com/en-us/library/cc264456.aspx" title="Windows 2008 Password Policy Settings">here</a> </div> 
    
    <p>
      After the initial login there are a couple key things we should do prior to starting the update cycle. First we are going to install the VMWare tools to make the interface easier to use and second, we are going to increase the screen resolution.
    </p>
    
    <div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
      <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/vmtools_host.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/vmtools_host.png" title="Host Server - Installing VMWare Tools" /></a><br /> Installing VMWare tools &#8211; Server OS View
    </div>
    
    <div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
      <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/vmtools_guest.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/vmtools_guest.png" title="Guest Server - Installing VMWare Tools" /></a><br /> Installing VMWare tools &#8211; Guest OS View
    </div>
    
    <p>
      After these two tasks we will begin by applying service packs (none for Windows 2008 R2 at this time) and then windows updates. Windows 2008 offers a handy dashboard to manage these tasks on the first install and, being lazy, I&#8217;m not going to turn it down.
    </p>
    
    <div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
      <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/setup_dashboard.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/setup_dashboard.png" title="Server Dashboard" /></a><br /> Windows Server &#8220;Configuration&#8221; Dashboard
    </div>
    
    <div class="hint">
      If you haven&#8217;t worked with server much in the past you may be surprised by a strange dialog when you attempt to restart your system. When you shutdown or reboot a server it asks you to provide a reason and note, I advise that you get in the habit of always typing up a short note on why you are shutting down or rebooting. It only takes a few seconds and one day it&#8217;s going to save you hours because these notes are entered into the event log before the system shuts down.
    </div>
    
    <p>
      Next I am going to glance through the features and see if there any others that I would want installed on all of my servers. In this case the only one I can see if SNMP, so a quick installation and configuration and we are ready to move on.
    </p>
    
    <div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
      <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/snmp_setup.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/snmp_setup.png" title="Server Features - Configuring SNMP" /></a><br /> Server Features &#8211; Configuring SNMP
    </div>
    
    <div class="hint">
      Installing SNMP services as part of your basic image gives you the opportunity to ensure you have consistent coverage and passwords across all your systems, which in turn makes deployment easier when it comes time to point your network or systems monitoring software at the new server.
    </div>
    
    <p>
      The step we might normally move on to in a production or corporate environment is installation of base antivirus, client license, or and inventory management software client. This is an excellent time to install software packages that are part of your standard or that can be pre-installed and updated later. Another consideration would be whether you need to set up VNC, RDP, or another remoting technology.
    </p>
    
    <p>
      The last item on my personal setup list is to assign a background image and set up BGInfo. Having a common background and system information on every server is extremely handy and helps show critical information at a glance (like disk usage or IP addresses). An easy solution is a utility called BGInfofrom SysInternals. BGInfo is <a href="http://technet.microsoft.com/en-us/sysinternals/bb897557.aspx" title="BGInfo at Technet">a free download</a> and there are many examples and articles available on the internet for setting it up.
    </p>
    
    <div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
      <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/bginfo_background.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/bginfo_background.png" title="Server Dashboard" /></a><br /> Windows Server &#8220;Configuration&#8221; Dashboard
    </div>
    
    <div class="mylab">
      Once upon a time I had scripts and standard layouts to add other interesting information to the background (such as uptime counters and such), but that can wait for another day. For this initial example I have grouped the information in a way that is meaningful for me, but haven&#8217;t done anything fancy.
    </div>
    
    <div class="hint">
      If you run into difficulties on 2008 R2 with permissions then follow the instructions posted near the bottom by prrawls here: <a href="http://forum.sysinternals.com/printer_friendly_posts.asp?TID=17427" title="View the post about BGInfo on 2008 R2">SysInternals Forum Post</a>
    </div>
    
    <div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
      <a href="http://www.tiernok.com/LTDBlog/labsetup/orig/done.png" target="_new"><img src="http://www.tiernok.com/LTDBlog/labsetup/done.png" title="Last Shutdown of the Basic Image" /></a><br /> Last Shutdown of the Basic Image
    </div>
    
    <h2>
      Basic Image Done
    </h2>
    
    <p>
      The basic server is complete and ready to serve as the foundation for the rest of the lab. In coming weeks we will use this basic image to create more systems, such as a basic database server and a domain controller.
    </p>
    
    <p>
      Additional Links:<br /> <a href="/index.php/DataMgmt/DBAdmin/MSSQLServerAdmin/virtual-lab-creating-a-basic-sql-2008-r2" title="Head to the Basic SQL 2008 R2 Install">Virtual Lab: Basic SQL 2008 R2 Virtual Machine</a><br /> <a href="http://wiki.ltd.local/index.php/Virtual_Lab" title="View the wiki entry">Virtual Lab entry on the LTD Wiki</a>
    </p>

 [1]: http://twitter.com/onpnt "Ted on Twitter"
 [2]: /index.php/All/?disp=authdir&author=68 "Ted's blogs here"
 [3]: /index.php/ITProfessionals/EthicsIT/my-singing-not-so-good "SQL Saturday Chicago article"
 [4]: http://tiernok.com/LTDBlog/dr_beanie.png
 [5]: http://tiernok.com/LTDBlog/dr_wizard.png
 [6]: http://downloads.vmware.com/d/info/datacenter_downloads/vmware_server/2_0#drivers_tools "Available from VMWare Server Tools section"
 [7]: http://www.microsoft.com/windowsserver2008/en/us/system-requirements.aspx "Windows 2008 R2 Requirements"
 [8]: http://www.microsoft.com/windowsserver2008/en/us/r2-editions-overview.aspx "See the feature comparison"