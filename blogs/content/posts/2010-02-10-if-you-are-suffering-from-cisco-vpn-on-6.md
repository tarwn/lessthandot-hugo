---
title: If you are suffering from Cisco VPN on 64 bit Windows then use the Shrew Soft VPN client
author: SQLDenis
type: post
date: 2010-02-11T00:09:50+00:00
ID: 698
url: /index.php/itprofessionals/softwareandconfigmgmt/if-you-are-suffering-from-cisco-vpn-on-6/
views:
  - 27676
rp4wp_auto_linked:
  - 1
categories:
  - Software and Configuration Management
tags:
  - 64 bit
  - software
  - tools
  - vpn

---
By now you probably have heard or seen the complains that there is no viable 64 bit VPN client that will work with a Cisco VPN. I myself either used a Virtual Machine (Virtual Box) or I have a dual boot system where I boot into the Fisher Price looking OS to VPN into work.

Yesterday my suffering ended. I downloaded the Shrew Soft VPN Client For Windows and it has been working great. It will even import Cisco pcf files so that you don't need to configure the VPN client if you already have the Cisco pcf files.

Download the Shrew Soft VPN client here: http://www.shrew.net/download/vpn
  
After it is installed start it up, click on _file_ and then _import_. You should be presented with a dialog like the one in the screenshot below. Change the dropdown to Cisco PCF file and navigate to the location where your files are. Repeat the process for each PCF file you have.

<div>
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals//Import.png" alt="" title="" width="630" height="434" />
</div>

After you have imported everything you need, click on one of the connections with a blue padlock icon and after that click on connect

<div>
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals//connect.png" alt="" title="" width="415" height="320" />
</div>

<div>
  <p>
    Fill in you username and password, click connect and you should see the following screen
  </p>
  
  <p>
    <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/ITProfessionals//Connected.png" alt="" title="" width="307" height="308" /></div> 
    
    <p>
      If everything is successful you should see something like the following in the text area above the credentials
    </p>
    
    <p>
      <em>config loaded for site 'connectDC.pcf'<br /> configuring client settings...<br /> attached to key daemon ...<br /> peer configured<br /> iskamp proposal configured<br /> esp proposal configured<br /> client configured<br /> local id configured<br /> pre-shared key configured<br /> bringing up tunnel ...<br /> network device configured<br /> tunnel enabled</em>
    </p>
    
    <p>
      If something is wrong you will see a message indicating a status in the same text area.
    </p>
    
    <p>
      Documentation is also available here: http://www.shrew.net/static/help-2.1.x/vpnhelp.htm
    </p>
    
    <p>
      The Shrew Soft VPN client is free but if it solves a problem for you consider donating something.
    </p>
    
    <p>
      That is it for this post, hopefully I will save a person or two from this 64 bit VPN hell.
    </p>