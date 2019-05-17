---
title: Patch for the ASP.NET Padding Oracle vulnerability has been released
author: SQLDenis
type: post
date: 2010-09-28T21:24:58+00:00
ID: 910
excerpt: |
  If you are running an ASP.NET, ASP.NET MVC or Sharepoint site, grab the patch for the ASP.NET Padding Oracle vulnerability 
  
  Some info:
  This security update resolves a publicly disclosed vulnerability in ASP.NET. The vulnerability could allow informa&hellip;
url: /index.php/webdev/serverprogramming/aspnet/patch-for-the-asp-net-padding-oracle-vul/
views:
  - 15322
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
tags:
  - asp.net
  - asp.net mvc
  - mvc
  - patch
  - sharepoint
  - vulnerability

---
If you are running an ASP.NET, ASP.NET MVC or Sharepoint site, grab the patch for the ASP.NET Padding Oracle vulnerability 

Some info:

> This security update resolves a publicly disclosed vulnerability in ASP.NET. The vulnerability could allow information disclosure. An attacker who successfully exploited this vulnerability could read data, such as the view state, which was encrypted by the server. This vulnerability can also be used for data tampering, which, if successfully exploited, could be used to decrypt and tamper with the data encrypted by the server. Microsoft .NET Framework versions prior to Microsoft .NET Framework 3.5 Service Pack 1 are not affected by the file content disclosure portion of this vulnerability.

And a little more info:

> An information disclosure vulnerability exists in ASP.NET due to improper error handling during encryption padding verification. An attacker who successfully exploited this vulnerability could read data, such as the view state, which was encrypted by the server. This vulnerability can also be used for data tampering, which, if successfully exploited, could be used to decrypt and tamper with the data encrypted by the server. Note that this vulnerability would not allow an attacker to execute code or to elevate their user rights directly, but it could be used to produce information that could be used to try to further compromise the affected system. In Microsoft .NET Framework 3.5 Service Pack 1 and above, **this vulnerability can also be used by an attacker to retrieve the contents of any file within the ASP.NET application, including web.config.**

Get it here: http://www.microsoft.com/technet/security/bulletin/ms10-070.mspx

The Padding Oracle Exploit Tool is available here http://netifera.com/research/ in case you want to see how it works

Here is a video of the attack in action
  
{{< youtube id="yghiC_U2RaM" autoplay="false" >}}

**Again, get the patch here:** http://www.microsoft.com/technet/security/bulletin/ms10-070.mspx