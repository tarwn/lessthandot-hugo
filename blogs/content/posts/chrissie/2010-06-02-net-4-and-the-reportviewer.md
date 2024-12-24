---
title: .Net 4 And the reportviewer
author: Christiaan Baes (chrissie1)
type: post
date: 2010-06-02T10:31:25+00:00
ID: 806
url: /index.php/desktopdev/mstech/net-4-and-the-reportviewer/
views:
  - 5464
categories:
  - Microsoft Technologies
tags:
  - .net 4.0
  - rdlc
  - reportviewer
  - vb.net

---
Switching to a new framework can be fun at times. 

This time I had a little problem with the Reportviewer.

> Report execution in the current AppDomain requires Code Access Security policy which is off by default in .NET 4.0 and later. Enable lagacy CAS policy or execute the report in the sandbox AppDomain.

I like the and later part.

Here is the printscreen, just in case you don&#8217;t believe me.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Reportviewer/ReportviewerError.png" alt="" title="" width="497" height="184" />
</div>

Solving this took some time. Finding out that they changed this is easy. Finding out how to solve it is a little less documented. 

In the end I found that it is this line that caused the problem.

```vbnet
_PreviewForm.Viewer.LocalReport.AddTrustedCodeModuleInCurrentAppDomain("rbarcode20, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null")
            ```
Well to be honest it had a green squigle underneath it to say that AddTrustedCodeModuleInCurrentAppDomain was deprecated and that I had to use AddFullTrustModuleInSandboxAppDomain all the time. But I thought it was just trying to annoy me.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Reportviewer/ReportviewerError2.png" alt="" title="" width="990" height="20" />
</div>

So I changed it to this.

```vbnet
Dim asm = Assembly.Load("rbarcode20, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null")
            Dim asm_name = asm.GetName()
            _PreviewForm.Viewer.LocalReport.AddFullTrustModuleInSandboxAppDomain(New System.Security.Policy.StrongName(New StrongNamePublicKeyBlob(asm_name.GetPublicKeyToken), asm_name.Name, asm_name.Version))
            ```
which was based on [this][1].

I find it weird that you have to give it a StrongName while the assembly is not Strongly named. For version 5 I would like an extra overload for AddFullTrustModuleInSandboxAppDomain that takes a string (TIA)

 [1]: http://devportal.hu/forums/p/8952/22313.aspx