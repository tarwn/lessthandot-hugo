---
title: Failed to access IIS metabase or why it is important to do things in the correct order
author: SQLDenis
type: post
date: 2008-12-04T12:11:17+00:00
url: /index.php/webdev/webdesigngraphicsstyling/failed-to-access-iis-metabase-or-why-it/
views:
  - 8921
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - asp.net
  - iis
  - sql server 2008
  - visual studio 2008

---
So yesterday I got a new machine at work. I decided to only install SQL Server 2008 and Visual Studio 2008 on this box. First I installed Visual Studio 2008, after that I installed Visual Studio 2008 Service Pack 1 and then I installed SQL Server 2008. I still remember when I tried to install SQL Server 2008 RTM on my older machine with Visual Studio 2008 and it wouldn&#8217;t do it because you needed SP1 for the framework first, this got released 2 days later or so.

So then I noticed that IIS was not installed on this box, of course you need the CD/DVD to add components to the Operating System. I went to MSDN, downloaded the Operating System, installed IIS and thought I was in business.

Do you see the problem yet?

I loaded up the website that runs on my machine and got the following message

**Failed to access IIS metabase**

Failed to access IIS metabase? What is that? Since I am lucky that I work with this Ukranian ASP.NET expert he told me to paste the following into the Run command from the Start menu

**%windir%Microsoft.NETFrameworkv2.0.50727aspnet_regiis.exe -i**

The version (v2.0.50727) might be different on your machine, it worked like a charm on mine.