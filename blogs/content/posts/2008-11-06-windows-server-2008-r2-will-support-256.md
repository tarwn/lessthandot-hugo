---
title: Windows Server 2008 R2 Will Support 256 Logical Processors
author: SQLDenis
type: post
date: 2008-11-06T16:17:27+00:00
url: /index.php/sysadmins/os/windows-server-2008-r2-will-support-256/
views:
  - 4518
rp4wp_auto_linked:
  - 1
categories:
  - Operating Systems
tags:
  - multicore
  - windows server 2008 r2

---
Windows Server 2008 R2 will support up to 256 logical processors, up till now Windows only supports 64 logical processors, that means you can&#8217;t even run that fancy Intel 80 core chip that they showed a while back.

Since I am a SQL Server guy this is going to be interesting, wonder if you can get an execution plan that uses 100+ processors. Imagine you pay per CPU? That would be a ton of money.

> **PressPass: How does Windows Server 2008 R2 differ from the previous version? What are the most notable new features?**
  
> Laing: We think it’s important to give customers a predictable timetable to plan for the next versions of our server technologies. We are working on an update release for Windows Server, named Windows Server 2008 R2, which is in line with the release cadence Bob Muglia outlined several years ago. As part of this update, we are integrating the latest service and feature packs with some new technology investments focused around four categories: virtualization, management, scalability and the Web.
> 
> From a virtualization standpoint, we’re building on our state-of-the-art virtualization technology with a newer version of our Hyper-V hypervisor technology as well as some new features that customers have been asking us for, such as Live Migration. This feature, which is included with Windows Server 2008 R2 at no additional charge, lets you move a running workload from one machine to another in milliseconds, with no loss of performance from the user’s point of view.
> 
> On the management front, Windows Server 2008 R2 will be a foundation for datacenter automation. We are making multiple improvements that give customers the reins to truly manage their servers the way they desire, whether that is locally or remotely, via a graphical user interface (GUI) or from the command line via Windows PowerShell. We are also making improvements to help customers reduce and better manage their datacenter power consumption. Windows Server 2008 R2 can automatically turn processor cores on and off based on the workload of the system, or reduce the power consumption by adjusting processor speed.
> 
> Another area of innovation in Windows Server 2008 R2 is the ability to more easily administer and support Web applications on a streamlined Web platform. We’ve integrated Internet Information Services 7.0 (IIS) manager extensions to make it simpler to administer local and remote Web servers, and added support for ASP.NET and PHP to the Server Core.
> 
> And finally, we continue to invest in scalability. In Windows Server 2008 R2, we have built in support for up to 256 logical processors, which will allow our customers to more fully exploit today’s powerful CPUs, deploying only the features they choose and scaling those solutions to meet their organization’s needs.

Very interesting, I already reported that Windows Server 2008 R2 will be 64 bit only.

Read the complete Q&A here: http://www.microsoft.com/presspass/features/2008/nov08/11-06winserverr2.mspx