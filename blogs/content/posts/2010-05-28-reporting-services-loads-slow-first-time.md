---
title: Report Manager / Report Server Loading Slow Initially
author: Ted Krueger (onpnt)
type: post
date: 2010-05-28T09:47:45+00:00
ID: 800
excerpt: I've been approached on the web in the SQL Community and in person several 
  times with the question; my Reporting Services instances loads Report Manager 
  extremely slow.  In some cases, nothing loads or I give up on both Report Manager 
  and connecting to Report Server.  Why?
url: /index.php/datamgmt/datadesign/reporting-services-loads-slow-first-time/
views:
  - 20469
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin
tags:
  - report manager
  - report server
  - sql server reporting services
  - ssrs

---
I've been approached on the web, in the SQL Community and in person several times with the question: “My Reporting Services instance loads Report Manager extremely slow. Why?”

There are a few common primary causes of this. One is the instance being located on a DMZ and behind policies that cause the slow load times. I'm not a network guru and haven't played in that field for 7-8 years so I can't give you a lot of ideas to fix it. I can say, check it if you are on that landscape and getting the slow load times.

The other cause comes down to locally installed firewalls and virus scan programs on the server (or machine you have SSRS installed on) blocking some or all access for Reporting Services, certain ports and ASP.NET. Firewalls are the devil from the inside out. I hate to say it, being the security crazed DBA I am but they do cause some headaches when working with features like Reporting Services that look to Internet Explorer for assistance in loading. 

I was fortunate enough to purchase a new laptop recently and installed my usual Developer Edition landscape. This included Reporting Services 2008. This gave me a chance to write this and recreate the exact situation many of you may fall into. 

## _So let's fix it together_

On my new Dell XPS I have McAfee Security installed. It came with the laptop and isn't all that bad. Does the job! But, when I open my Report Manager session, it takes around 5 minutes to load. Wow! I'm not a patient DBA. I like measuring things in nanoseconds. It is the foundation of why I purchase hardware at work that is way overboard and scalable. So my first inclination is to turn to the Windows Firewall. I know a lot of people may disagree with me, but I turn it off. It is good and I understand the concept behind it, but in our world, it can cause more problems than it's worth. I'm behind my Cisco firewall and the McAfee firewall. I think I'm ok for now until a script kiddy reads this 😉

I opened the McAfee Security Center and navigated to the location I assume will be causing the problem — the firewall configuration. In this configuration I see that the Firewall protection is enabled and the option for configuring it is there. Upon opening this configuration I am presented with a listing of programs/files that have particular “access” settings. 

In this listing I quickly notice that my SQL Server Analysis Service instance is blocked. Fixed that! 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/manager_slow.gif" alt="" title="" width="557" height="294" />
</div>

After searching the listing farther down, I find the settings for Reporting Services (and Report Builder). The access is set to Outbound-Only Access. This should be Full Access for the instance to function completely. It is important to note that this would not stop functionality of Reporting Services out of the box typically. You can run and develop SSRS on an island successfully. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/manager_slow2.gif" alt="" title="" width="557" height="360" />
</div>

You can see there is an option to, “Allow Access” in the Action window. I selected this and rebooted the laptop to clear all cache and load of the Report Manager. I did this because the initial load is commonly the only slow period when ASP.NET starts. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DataMgmt/manager_slow3.gif" alt="" title="" width="540" height="257" />
</div>

The largest problem is the firewall not allowing the action to be performed entirely. 

I also found that port 1433 was being heavily scanned along with port 80. Next I found the “real-time” scanning settings and had to set the required files and services to Trusted. I basically found a few odds and ends just short of completely turning it off. I'm not recommending that of course. I'm recommending finding the setting your exact situation requires. All virus and firewalls are different and it isn't feasible to cover them all here. 

Again, another reboot is required to determine if the settings fixed my problem 

## _Conclusion_

My firewall configurations fixed the local Report Manager initial loading time problem and allowed ASP.NET and SSRS to do what it needed to without slowing the process. At the same time I found my entire SSAS instance was completely blocked. So if you find yourself with Report Manager loading in about 5 minutes (give or take an hour), try disabling the local firewall and see if it resolves the problem down to normal initial load times. Then enable it and dig into the configuration settings to allow Reporting Service and ASP.NET to function correctly. Normal load times are around 30-45 seconds after a complete restart from this lab. In other resource intense servers that will be quicker. If it goes much over that, I would start looking into the problem. 

Last, I highly recommend always digging into BOL before going crazy and breaking your servers. There is a page dedicated to short descriptions on possible performance issues. Primarily surrounding, slow performance.
  
http://technet.microsoft.com/en-us/library/ms345220.aspx