---
title: 'Virtual Lab: Creating a 2008 R2 Domain Controller'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-05-28T16:38:15+00:00
ID: 790
excerpt: 'A virtual domain controller (DC) can be both a powerful and lightweight addition to your virtual lab. A central AD implementation not only gives you another set of services to work with, it also provides a more realistic environment with the power of do&hellip;'
url: /index.php/sysadmins/os/windows/virtual-lab-creating-a-2008-r2-domain-co/
views:
  - 28930
rp4wp_auto_linked:
  - 1
categories:
  - 2008 Server
  - Windows

---
<div class="acc_header">
  A virtual domain controller (DC) can be both a powerful and lightweight addition to your virtual lab. A central AD implementation not only gives you another set of services to work with, it also provides a more realistic environment with the power of domain accounts and policies that will be useful when you start working with multi-server setups. This article will walk through the creation of a basic 2008 R2 Domain Controller.<br /> <br /> <label>Technical Area:</label> Accidental Systems Administrator<br /> <label class="diff">Level of Difficulty: </label><img src="http://tiernok.com/LTDBlog/dr_basic.png" alt="Basic Difficulty" /><br /> <label>Additional Articles:</label><a href="http://wiki.ltd.local/index.php/Virtual_Lab" title="View the wiki entry">Virtual Lab entry on the LTD Wiki</a>
</div>



## Terminology

There are some terms that are commonly used in relation to AD or a Domain Controller that will appear in this and other documents:

Domain Controller
:   The server or servers responsible for handling authentication and informational requests for users/systems on the domain

Forest
:   Domains are represented as trees of directory information, a forest is the group of all trees in the organization

Kerberos
:   Kerberos is an authentication protocol used to allow systems on an un-trusted network a method to prove their identity to one another

FQDN
:   Fully Qualified Domain Name &#8211; the fully spelled out domain name for a domain, for example mydomain.com

<div class="hint">
  There has been a critical change to this article due to a new piece of information that I didn't previously know. Rather than use the base windows image as the DC for your lab you should go ahead and install this machine from scratch to ensure it doesn't have a duplicate SID. In prior versions we could (sort-of) change a SID but that's not really the case anymore. This is really only a big deal for the DC because the SIDs for users from the DC (domain accounts) need to not match SIDs for local users/groups/etc.<br /> More details can around duplicate SIDs, 2008 R2, and similar topics are available in <a href="http://blogs.technet.com/b/markrussinovich/archive/2009/11/03/3291024.aspx" target="_blank">this article from Mark Russinovich</a>.
</div>

## Setting up VM

First we need to create a virtual machine to house the future domain controller (DC). In the [first article][1] we walked through the process of creating a basic VM. We will follow those same steps to create the VM for our DC except once we have Windows installed we will customize it further than we did with the original VM.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/0_AddVirtualMachine.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/0_AddVirtualMachine.png" alt="Add Virtual Machine, Step 0" /></a><br /> Creating the Virtual Machine
</div>

For the time being this system is going to be connected to the Host via the existing Bridged connection. At a later point in time we will look at creating virtual networks in VMWare and build out a more defined network. 

<div class="mylab">
  Because I don't intend on having a heavy workload on this server or installing any additional services beyond AD and DNS, I have only allocated 512MB of memory to the server. If it becomes noticeably sluggish servicing my tiny domain then I will bump it up to 1024.
</div>

## Configuring the OS

Once the virtual machine is created, configured in VMWare, and Windows has been installed, it's time to move on to the Windows configuration. Some of these will be the same as our prior article and several will be new additions to prep the server for Active Directory.

First we will want to configure the server name and then assign it a new static IP Address on the local network.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/1_startup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/1_startup.png" alt="Changing the System Name" /></a><br /> Changing the System Name
</div>

After changing the name we'll open the network settings by either selecting the “Configure Networking” link on the Initial Configuration dashboard (above) or clicking the network icon in the tool tray at the bottom right, followed by “Open Network and Sharing”, and then “Change adapter settings” near the top right of the screen. With the “Network Connections” screen open we can right click the network adapter and enter the “Properties” pane. Select the “Internet Protocol Version 4” item from the list and press properties to access the IP configuration panel. 

At this point we need to select a static IP address that isn't already in use on our network and isn't in the DHCP range for our network. After selecting an IP Address we enter the static IP address, enter the gateway server or switches address, and then enter the servers address as the primary DNS entry and our switch or external DNS as the secondary one.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/2_startup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/2_startup.png" alt="Configuring the IP Address" /></a><br /> Configuring the IP Address
</div>

<div class="mylab">
  My home network has defined ranges and already has a DNS server, so these servers will all receive addresses in the 51-99 range and they will have the non-virtual DNS server listed as a secondary DNS.
</div>

<div class="hint">
  It is possible to install Active Directory and the DNS server on a system without a static IP address and trust that it will assign itself the same address over time, but I don't recommend it as your clients will eventually not be able to find their DNS server and than pain and suffering will ensue.
</div>

The last step to preparing your server for Active Directory is planning. In a non-lab installation we would want to put some forethought into how many clients we intend to support with Directory Services, whether there will be secondary DCs on the network, etc. I have an older link for [capacity planning for AD on Windows 2003][2], but unfortunately I have not been able to find a comparable article for 2008 yet.

## Installing Active Directory

In Windows 2008, Active Directory has expanded to take on a wider range of roles and capabilities. The portion we formerly considered to be AD, directory management and communications, is now called Active Directory Domain Services (AD DS). Other services include Active Directory Certificate Services (AD CS), Active Directory Federation Services (AD FS), Active Directory Lightweight Directory Services (AD LDS), and Active Directory Rights Management Services (AD RMS). Today we will be concentrating on AD DS, though I will continue to refer to as AD throughout the rest of the post due to bad habits and years of practice.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/0_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/0_setup.png" alt="Assigning Server Roles" /></a><br /> Assigning Server Roles
</div>

To start the installation and setup process, we will refer back to the Initial Configuration dashboard (click the pinned shortcut on the taskbar if you closed it already). Under section 3 there is a “Roles” category that will allow us to easily assign the necessary roles to our server.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/1_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/1_setup.png" alt="Assigning Server Roles" /></a><br /> Assigning Server Roles
</div>

### Installing the Active Directory Role

The first page of the “Add Roles Wizard” presents us with some friendly reminders to set a static IP Address (check), ensure the Administrator account has a strong password (check), and make sure the server is up to date on Windows Updates (check). 

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/2_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/2_setup.png" alt="Assigning Server Roles" /></a><br /> Assigning Server Roles
</div>

Next we move on to the Roles selection page. As promised, we see a number of active directory services listed, the option for today being AD DS.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/3_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/3_setup.png" alt="Installing AD DS" /></a><br /> Installing AD DS
</div>

On selecting the AD DS option the wizard runs a quick validation and notices that our server does not appear to have a necessary component, in this case .Net Framework 3.5. 

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/4_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/4_setup.png" alt="Installing AD DS" /></a><br /> Installing AD DS &#8211; Prerequisites
</div>

After choosing to install the prerequisite (a difficult and time consuming decision, or perhaps not) and pressing next on the main screen, we are provided with a list of “Things to Note” as well as links to additional information, saving me the time of writing and linking them up myself.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/5_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/5_setup.png" alt="Installing AD DS" /></a><br /> Installing AD DS &#8211; Things to Note
</div>

A short confirmation displays the items we are installing, including the noted prerequisite above and a reminder that directory services won't truly be functional until we run “dcpromo”.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/6_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/6_setup.png" alt="Installing AD DS" /></a><br /> Installing AD DS &#8211; Confirmation
</div>

After the installation and verification is complete, we are presented a handy link to launch dcpromo.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/7_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/7_setup.png" alt="Installing AD DS" /></a><br /> Installing AD DS &#8211; Wizard Complete
</div>

<div class="hint">
  If you accidentally close the window before pressing the link or need to access dcpromo later, the start menu's run input will recognize “dcpromo” and find the requisite shortcut for you.
</div>

### AD DS Installation Wizard (DCPromo)

The dcpromo wizard will complete the installation of AD DS on the server. 

On the first step we are presented with the option to “Use Advanced Mode Setup”. This option will cause several additional wizard windows to appear throughout the process that would have defaulted behind the scenes if it were not checked. Press the “advanced mode installation” hyperlink to get a help document about the additional options presented in advanced mode.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/7_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/7_setup.png" alt="DCPromo" /></a><br /> DCPromo &#8211; Beginning
</div>

After an informational screen about the suggested cryptography option and systems that may not support it, we are presented with the option to add a domain to an existing forest or to create a new domain under a new forest. This being our first DC in the virtual lab, we will be creating a new forest for our domain.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/8_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/8_setup.png" alt="DCPromo" /></a><br /> DCPromo &#8211; New Forest and Domain
</div>

Next type the fully qualified domain name (FQDN) for your domain. After typing this name the wizard will then scan to ensure the name is not already in use.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/9_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/9_setup.png" alt="DCPromo" /></a><br /> DCPromo &#8211; FQDN Entry
</div>

The wizard then prompts us to enter a Domain NetBIOS name, with a suggestion based on the name from the prior step.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/10_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/10_setup.png" alt="DCPromo" /></a><br /> DCPromo &#8211; NetBIOS Name
</div>

Next we'll set the functional level of our forest. The functional level defines the maximum level (and feature set) that our forest will support. When installing additional DCs in an existing forest or creating a business domain that you intend to support over the next ten years, this needs to be a well thought out decision. When installing in a virtual lab we will of course pick the highest possible level in less time than it took to write this paragraph.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/11_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/11_setup.png" alt="DCPromo" /></a><br /> DCPromo &#8211; Functional Level
</div>

<div class="hint">
  One of the best reasons for using 2008 R2 mode is the new “Recycle Bin” option. This option keeps deleted entries in memory and provides an option to restore them, providing a bit of a safety net and reducing the potential for having to use the AD Recovery Mode to recover a deleted set of accounts or objects.
</div>

<div class="mylab">
  Actually the Recycle Bin option worries me. I like admins (and myself) to be a little scared of their tools and nothing says scared like remembering that time you accidentally deleted something and spent recovering it manually. Oh well.
</div>

After another quick scan of the network will tell us if DNS is available and supports the necessary capabilities for the domain controller. In this case, since we are installing a primary DC, I'm going to suggest we install DNS on the same server.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/12_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/12_setup.png" alt="DCPromo" /></a><br /> DCPromo &#8211; DNS
</div>

After selecting to install the DNS server we receive a warning that a delegation for the DNS server cannot be created because the authoritative parent zone for the domain name I entered earlier could not be found. Translated, this means that the server went looking for an authoritative DNS entry for the _avl.local_ domain name I entered and determined that no one had ever heard of it. Since we are just now installing the DNS server that will recognize that name, this is to be expected and can be safely ignored. 

The next step is to provide a location for the database and files that will be used by AD DS. In a non-lab setup this needs to be carefully planned to ensure enough capacity is available ahead of time. This being a virtual lab, we will be accepting the defaults and allowing it to carve out space from the primary drive.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/13_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/13_setup.png" alt="DCPromo" /></a><br /> DCPromo &#8211; Database and File Locations
</div>

The Directory Services Restore password is used only when major restoration activities need to be undertaken. After entering the password we will add it to our password safe so it won't be forgotten. This password will hopefully never be needed (especially in a lab environment), but not writing it down will pretty much ensure our first, future restoration step will be getting past the lack of a password.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/14_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/14_setup.png" alt="DCPromo" /></a><br /> DCPromo &#8211; Restore Password
</div>

After a brief summary of the selected configurations and the option to export these settings for an unattended installation, we are ready to let the server start installing.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/15_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/15_setup.png" alt="DCPromo" /></a><br /> DCPromo &#8211; Installing
</div>

The installer wraps up with the common Finish screen and a request to reboot.

After rebooting, our “Initial Configuration Tasks” window confirms our new roles have been installed. 

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/BasicDC/16_setup.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/BasicDC/16_setup.png" alt="DCPromo" /></a><br /> Initial Configuration Tasks &#8211; Roles
</div>

## Managing the Domain

Our next post will cover basic administration topics for the domain, as we add our first SQL Server system to the domain, create a new administrative user, and create a service account for our SQL Server services.

<label>Additional Articles:</label>[Virtual Lab entry on the LTD Wiki][3]

 [1]: /index.php/SysAdmins/OS/building-the-virtual-lab-vmware-and-2008 "Building the Virtual Lab: VMWare and MS Windows 2008 R2"
 [2]: http://technet.microsoft.com/en-us/library/cc739022%28WS.10%29.aspx "Assess Hardware for AD on Windows 2003"
 [3]: http://wiki.ltd.local/index.php/Virtual_Lab "View the wiki entry"