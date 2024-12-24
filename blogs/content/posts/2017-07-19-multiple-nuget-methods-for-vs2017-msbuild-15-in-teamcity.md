---
title: Multiple NuGet Methods for VS2017 + MSBuild 15 in TeamCity
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-07-19T18:06:25+00:00
ID: 8697
url: /index.php/itprofessionals/softwareandconfigmgmt/multiple-nuget-methods-for-vs2017-msbuild-15-in-teamcity/
views:
  - 4033
rp4wp_auto_linked:
  - 1
categories:
  - Software and Configuration Management
tags:
  - nuget
  - teamcity
  - Visual Studio 2017

---
ASP.Net Projects and NuGet have been a moving target the last couple years. I have an ASP.Net Core project (.Net Framework) with several class libraries and had to work through a number of problems to get NuGet Restore working on a TeamCity CI server. Hopefully this will help someone else along the way. 

It turns out I have 3 situations:

  * ASP.Net Core uses the new [PackageReference][1] for packages instead of packages.json
  * Other C# Projects still use the packages.json method for packages
  * Solution packages (great for tooling) are (still) not supported after VS 2013 ([NuGet #522][2])

(the last couple years also saw the ill-fated project.json, which isn't represented here and may or may not be covered by one of these methods)

The software versions I am working with are:

  * TeamCity 2017.1
  * NuGet 4.1.0
  * Visual Studio 2017 Community
  * MS Build Tools 2017 (MSBuild 15)

I've outlined the issues I ran into and the individual Build Steps I used to workaround them.

## Some of the issues

**Issue #1: Restoring packages.json + PackageReference**

NuGet Restore is built into MSBuild 15 (`msbuild /t:restore`) to support the new PackageReference case. Supposedly NuGet.exe 4+ supports this out of the box, but I was unable to make this work ([NuGet Package Restore][3]):

> `msbuild /t:restore` Nuget 4.x+ and MSBuild 15.1+ with package references in project files only. nuget restore and dotnet restore both use this command for applicable projects. See NuGet pack and restore as MSBuild targets- restore target. 

It is possible to [use PackageReference with other project types][4], but you have to know to turn this on from the beginning. There is no migration option to change tracks with later. 

**Issue #2: MSBuild install location has moved**
  
MSBuild installs to a new folder with 2017 (`C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\MSBuild`), which means for a while my NuGet Restore commands were defaulting to MSBuild 14. The `-MsBuildPath` parameter let's you provide a path to a specific install, so we can at MSBuild 15 via it's new path. 

**Issue #3: Regular Package.json files plus Solution Package.son File**

When we run it against a `*.sln` file, NuGet.exe will find and run against each project's `package.json` file. Alternatively, you can also point NuGet.exe directly at a package.json file to install that specific file, which is a workaround for the no longer support solution packages. There is not an option (that I could find) to combine these into a single call.

**Issue #4: TeamCity only supports NuGet Restore of *.sln files**

TeamCity restricts the built-in NuGet Restore command to only run against files that end in ".sln". A separate flaw in this restriction was [pointed out to JetBrains in 2015][5], but has not been fixed as of TeamCity 2017.1.

This restriction prevents me from doing a clean NuGet Restore of my solution-level packages.json from issue #3.

## The Fixes

I ended up using 3 Build Steps to do NuGet restores in TeamCity for this one ASP.Net Core deployment.

<div id="attachment_8699" style="width: 882px" class="wp-caption aligncenter">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/07/TeamCityNuGetRestorePost.png" alt="TeamCity NuGet Restore Steps" width="872" height="239" class="size-full wp-image-8699" srcset="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/07/TeamCityNuGetRestorePost.png 872w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/07/TeamCityNuGetRestorePost-300x82.png 300w, https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2017/07/TeamCityNuGetRestorePost-768x210.png 768w" sizes="(max-width: 872px) 100vw, 872px" />
  
  <p class="wp-caption-text">
    TeamCity NuGet Restore Steps
  </p>
</div>


  
**1. Restore from package.json files**

I used the built-in Nuget Installer with the Restore option and the name of my solution file. I added an extra command line parameter to point it at MSBuild 15:
  
`-MsBuildPath "C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\MSBuild\15.0\Bin"`.

**2. Restore from PackageReferences**

For PackageReference, I used the MSBuild task in TeamCity with my Solution file as the Build file path and a target of "Restore" (same as running `msbuild /t:Restore`).

**3. Restore Solution-level packages**

A solution-level packages file is useful for solution-level build and deploy tools that aren't needed locally. Because TeamCity has an artifical constraint on only accepting *.sln files, I solved this with a `Command Line` runner type with the following script:

`%teamcity.tool.NuGet.CommandLine.DEFAULT%\\tools\\nuget.exe restore Deployment/packages.config -PackagesDirectory packages`

Deployment/packages.config is my solution-level packages file. The TeamCity variable resolves to the path of my default NuGet installation, which is 4.1.0 in this case.

## Final Words

This experience was roughly 3-5 times longer than I expected, given the number of ASP.Net deployments, TeamCity setups, and so on I've done over the years. The playing field feels littered with half-baked solutions that not only break backwards compatibility, but break compatibility across projects in the same solution using vanilla Visual Studio project templates.

 [1]: http://blog.nuget.org/20170316/NuGet-now-fully-integrated-into-MSBuild.html
 [2]: https://github.com/NuGet/Home/issues/522
 [3]: https://docs.microsoft.com/en-us/nuget/consume-packages/package-restore
 [4]: http://blog.nuget.org/20170316/NuGet-now-fully-integrated-into-MSBuild.html#what-about-other-project-types-that-are-not-net-core
 [5]: https://blog.jetbrains.com/teamcity/2013/08/nuget-package-restore-with-teamcity/#comment-177428