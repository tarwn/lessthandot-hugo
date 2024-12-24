---
title: Did you notice there is a new .Net framework version?
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-18T09:53:00+00:00
ID: 1180
excerpt: |
  This one slipped by me Microsoft .NET Framework 4 Platform Update 1. So we now have a new version of the .Net framework, but they didn't give it a new version number, they used some manager talk and a KB number. 
  And let's not forget that there is a cl&hellip;
url: /index.php/desktopdev/mstech/msaccess/accessformsreports/did-you-notice-there-is/
views:
  - 12262
categories:
  - Access Forms and Reports
tags:
  - .net

---
This one slipped by me [Microsoft .NET Framework 4 Platform Update 1][1]. So we now have a new version of the .Net framework, but they didn&#8217;t give it a new version number, they used some manager talk and a KB number.
  
And let&#8217;s not forget that there is a client profile with the same name, did I mention I hate the client profile? And why is it made the default?

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/5810_2E00_.NET-Framework-4-Platform-Update-1-New-Project.jpg?mtime=1305719488"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/5810_2E00_.NET-Framework-4-Platform-Update-1-New-Project.jpg?mtime=1305719488" width="550" height="302" /></a>
</div>

And it consist of three packages.

> The Microsoft .NET Platform Update 1 consists of three packages:
> 
> Microsoft .NET Framework 4 Platform Update 1 (KB2478063)
          
> This package contains the runtime files for the platform update. This package must be deployed on systems where applications that target the platform update are deployed.
      
> Multi-Targeting Pack for Microsoft .NET Framework 4 Platform Update 1 (KB2495638)
          
> This package contains reference assemblies and intellisense files for the platform update. This package is installed as part of the next package.
      
> Microsoft .NET Framework 4 Platform Update 1 – Design-time Package for Visual Studio 2010 SP1 (KB2495593)
          
> This package installs the previous two packages and configures Visual Studio 2010 SP1 with new .NET Framework targeting profiles, intellisense, and adds the state machine activities to the toolbox.

You can read the rest in the MSDN article.

And I&#8217;m [not the only one][2] feeling this was not a good idea.
  
And [here][3] .

 [1]: http://blogs.msdn.com/b/endpoint/archive/2011/04/18/microsoft-net-framework-4-platform-update-1.aspx
 [2]: http://feedproxy.google.com/~r/Devlicious/~3/2RB1iDibrSo/did-you-just-take-a-dump-on-standard-versioning-practices.aspx
 [3]: http://blog.maartenballiauw.be/post/2011/05/18/Microsoft-NET-Framework-4-Platform-Update-1-KB2478063-Service-Pack-5-Feature-Set-31-R2-November-Edition-RTW.aspx