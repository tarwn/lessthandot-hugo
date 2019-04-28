---
title: 'Chocolatey: apt-get for windows'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-24T14:30:00+00:00
ID: 1275
excerpt: |
  Introduction
  
  Today I read that nodejs was available on nuget. I wonder why that would be and what extra value that would add to my project. After all nuget is there to install dependent package to my project and keep them up to date. Then I found out&hellip;
url: /index.php/desktopdev/mstech/chocolatey-apt-get-for-windows/
views:
  - 12871
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - chocolatey
  - nuget

---
## Introduction

Today I read that nodejs was available on [nuget][1]. I wonder why that would be and what extra value that would add to my project. After all nuget is there to install dependent package to my project and keep them up to date. Then I found out that it needed the [chocolatey][2] package. The chocolatey package is not really a typical nuget package either. It just uses nuget to get installed. But it peaked my interest. So I installed it.

## installing chocolatey

so I went to nuget and opened a solution. Nuget won&#8217;t download packages when not in a solution. And then I did the sensible thing and opened the nuget console and entered this command.

> install-package chocolatey

With this as the result.

> Successfully installed &#8216;chocolatey 0.9.8.4&#8217;.
  
> ========================
  
> Chocolatey
  
> ========================
  
> Welcome to Chocolatey, your local machine repository for NuGet. Chocolatey allows you to install application packages to your machine with the goodness of a #chocolatey #nuget combo.
  
> Application executables get added to the path automatically so you can call them from anywhere (command line/powershell prompt), not just in Visual Studio.
> 
> Lets get Chocolatey!
  
> &#8212;&#8212;&#8212;-
  
> Visual Studio &#8211;
  
> &#8212;&#8212;&#8212;-
  
> Please run Initialize-Chocolatey one time per machine to set up the repository.
  
> If you are upgrading, please remember to run Initialize-Chocolatey again.
  
> After you have run Initiliaze-Chocolatey, you can safely uninstall the chocolatey package from your current Visual Studio solution.
  
> &#8212;&#8212;&#8212;-
  
> Alternative NuGet &#8211;
  
> &#8212;&#8212;&#8212;-
  
> If you are not using NuGet in Visual Studio, please navigate to the directory with the chocolateysetup.psm1 and run that in Powershell, followed by Initialize-Chocolatey.
  
> Upgrade is the same, just run Initialize-Chocolatey again.
  
> &#8212;&#8212;&#8212;-
  
> Once you&#8217;ve run initialize or upgrade, you can uninstall this package from the local project without affecting your chocolatey repository.
  
> ========================

So I now have chocolatey and I can remove the package from my project. 

Next step seems to be to initialize it.

With this as the result.

> We are setting up the Chocolatey repository for NuGet packages that should be at the machine level. Think executables/application packages, not library packages.
  
> That is what Chocolatey NuGet goodness is for.
  
> The repository is set up at &#8216;C:NuGet&#8217;.
  
> The packages themselves go to &#8216;C:NuGetlib&#8217; (i.e. C:NuGetlibyourPackageName).
  
> A batch file for the command line goes to &#8216;C:NuGetbin&#8217; and points to an executable in &#8216;C:NuGetlibyourPackageName&#8217;.
> 
> Creating Chocolatey NuGet folders if they do not already exist.
> 
> Mode LastWriteTime Length Name
  
> &#8212;- &#8212;&#8212;&#8212;&#8212;- &#8212;&#8212; &#8212;-
  
> d&#8212;- 24/07/2011 18:21 bin
  
> d&#8212;- 24/07/2011 18:21 lib
  
> d&#8212;- 24/07/2011 18:21 chocolateyInstall
  
> Copying the contents of &#8216;F:mydocumentsvisual studio 2010ProjectsMvcApplication1packageschocolatey.0.9.8.4toolschocolateyInstall&#8217; to &#8216;C:NuGet&#8217;.
  
> Creating &#8216;C:NuGetbinchocolatey.bat&#8217; so you can call &#8216;chocolatey&#8217; from anywhere.
  
> Creating &#8216;C:NuGetbincinst.bat&#8217; so you can call &#8216;chocolatey install&#8217; from a shortcut of &#8216;cinst&#8217;.
  
> Creating &#8216;C:NuGetbincinstm.bat&#8217; so you can call &#8216;chocolatey installmissing&#8217; from a shortcut of &#8216;cinstm&#8217;.
  
> Creating &#8216;C:NuGetbincup.bat&#8217; so you can call &#8216;chocolatey update&#8217; from a shortcut of &#8216;cup&#8217;.
  
> Creating &#8216;C:NuGetbinclist.bat&#8217; so you can call &#8216;chocolatey list&#8217; from a shortcut of &#8216;clist&#8217;.
  
> Creating &#8216;C:NuGetbincver.bat&#8217; so you can call &#8216;chocolatey version&#8217; from a shortcut of &#8216;cver&#8217;.
> 
> PATH environment variable does not have C:NuGetbin in it. Adding.
  
> <span class="MT_red">You cannot call a method on a null-valued expression.<br /> At F:mydocumentsvisual studio 2010ProjectsMvcApplication1packageschocolatey.0.9.8.4toolschocolateysetup.psm1:139 char:48<br /> + $hasStatementTerminator= $userPath.EndsWith <<<< ($statementTerminator) + CategoryInfo : InvalidOperation: (EndsWith:String) [], RuntimeException + FullyQualifiedErrorId : InvokeMethodOnNull</span>
> 
> Chocolatey is now ready.
  
> You can call chocolatey from anywhere, command line or powershell by typing chocolatey.
  
> Run chocolatey /? for a list of functions.

Not sure if the exception is going to e harmful but I guess I should try.

## installing nodejs

Now it is time to install nodejs.

So I typed in 

> chocolatey install nodejs

With this as the result.

> =====================================================
  
> Chocolatey (0.9.8.4) is installing nodejs (from https://go.microsoft.com/fwlink/?LinkID=206669) to &#8220;C:NuGetlib&#8221;
  
> =====================================================
  
> Package License Acceptance Terms
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Please run chocolatey /? for full license acceptance verbage. By installing you accept the license for the package you are installing&#8230;
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> NuGet
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Attempting to resolve dependency &#8216;chocolatey (= 0.9.8.4)&#8217;.
  
> Successfully installed &#8216;chocolatey 0.9.8.4&#8217;.
  
> Successfully installed &#8216;nodejs 0.5.2&#8217;.
> 
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Chocolatey Runner (CHOCOLATEY)
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Chocolatey Installation (chocolateyinstall.ps1)
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Looking for chocolateyinstall.ps1 in folder C:NuGetlibchocolatey.0.9.8.4
  
> If chocolateyInstall.ps1 is found, it will be run.
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Elevating Permissions and running powershell -NoProfile -ExecutionPolicy unrestricted -Command &#8220;& import-module -name C:NuGetchocolateyInsta
  
> llhelperschocolateyInstaller.psm1; . &#8216;C:NuGetlibchocolatey.0.9.8.4toolschocolateyInstall.ps1&#8242;&#8221;. This may take awhile, depending on the p
  
> ackage.
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Executable Batch Links
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Looking for executables in folder: C:NuGetlibchocolatey.0.9.8.4
  
> Adding batch files for any executables found to a location on PATH.
  
> In other words, the executable will be available from ANY command line/powershell prompt.
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Adding C:NuGetbinNuGet.bat and pointing to C:NuGetlibchocolatey.0.9.8.4toolschocolateyInstallNuGet.exe
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Chocolatey Runner (NODEJS)
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Chocolatey Installation (chocolateyinstall.ps1)
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Looking for chocolateyinstall.ps1 in folder C:NuGetlibnodejs.0.5.2
  
> If chocolateyInstall.ps1 is found, it will be run.
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Elevating Permissions and running powershell -NoProfile -ExecutionPolicy unrestricted -Command &#8220;& import-module -name C:NuGetchocolateyInsta
  
> llhelperschocolateyInstaller.psm1; . &#8216;C:NuGetlibnodejs.0.5.2toolschocolateyInstall.ps1&#8242;&#8221;. This may take awhile, depending on the package
  
> .
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Executable Batch Links
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Looking for executables in folder: C:NuGetlibnodejs.0.5.2
  
> Adding batch files for any executables found to a location on PATH.
  
> In other words, the executable will be available from ANY command line/powershell prompt.
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> Adding C:NuGetbinnode.bat and pointing to C:NuGetlibnodejs.0.5.2toolsnode.exe
  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
> =====================================================
  
> Chocolatey has finished installing nodejs
  
> =====================================================

And this along the way.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey1.png?mtime=1311524721"><img alt="" src="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey1.png?mtime=1311524721" width="961" height="660" /></a>
</div>

And now I have to test that node is there I guess.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey2.png?mtime=1311524871"><img alt="" src="/wp-content/uploads/users/chrissie1/chocolatey/chocolatey2.png?mtime=1311524871" width="707" height="423" /></a>
</div>

And yes that counts as proof. 

And there are [other ways to install][3] chocolatey.

## Conclusion

Yep it works, now I just have to see how to get the new version of nodejs when it comes out and how chocolatey will cover this.

Edit: @ferventcoder aka Rob reynolds told me there is a [wiki][4] that covers all this. you can just type cver nodejs to check if there is a new version and cup to do the update.

 [1]: http://nuget.org/
 [2]: http://nuget.org/List/Packages/chocolatey
 [3]: https://github.com/ferventcoder/chocolatey/wiki/Installation
 [4]: https://github.com/ferventcoder/chocolatey/wiki/CommandsReference