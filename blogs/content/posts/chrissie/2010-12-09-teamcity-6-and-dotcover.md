---
title: TeamCity 6 and Dotcover
author: Christiaan Baes (chrissie1)
type: post
date: 2010-12-09T07:34:41+00:00
ID: 970
excerpt: |
  Introduction
  
  The new Teamcity 6 has dotcover support for absolutely free. So you should use it. Here is how I set it up and what problems I had.
  
  How to set it up.
  
  It's insanely simple. In your Nunit build step go to the bottom and select JetBra&hellip;
url: /index.php/itprofessionals/softwareandconfigmgmt/teamcity-6-and-dotcover/
views:
  - 4429
categories:
  - Software and Configuration Management
tags:
  - dotcover
  - teamcity 6

---
## Introduction

The new Teamcity 6 has dotcover support for absolutely free. So you should use it. Here is how I set it up and what problems I had.

## How to set it up.

It&#8217;s insanely simple. In your Nunit build step go to the bottom and select JetBrains dotCover as your .Net Coverage tool. Select your runtime and version and you&#8217;re done.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/TeamCity6_dotcover_1.png" alt="" title="" width="733" height="651" />
</div>

Simple as pie.

## Problem

However mine did not create a report but it did add an error to the build log.

You can find the Build log tab when you click on the build.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/TeamCity6_dotcover_2.png" alt="" title="" width="740" height="297" />
</div>

There I found this.

> Generate dotCover report (20s)
  
> [16:39:32]: [Generate dotCover report] dotCover exited with code: -1
  
> [16:39:32]: [Generate dotCover report] dotCover returned non-zero exit code.
  
> [16:39:34]: Generate dotCover HTML report (2s)
  
> [16:39:34]: [Generate dotCover HTML report] Loading dotCover report file&#8230; (2s)
  
> [16:39:37]: [Loading dotCover report file&#8230;] Failed to read dotCover report from: C:TeamCitybuildAgenttempbuildTmpdotCover4308003091714464036Report. XML document structures must start and end within the same entity.
  
> [16:39:38]: Failed to compute .NET Coverage statistics for dotCover report generator&#8217; XML document structures must start and end within the same entity.

the xml file is there and is 50 MB big and does not end in the correct way. Like dotcover did not write out the XML correctly.

it ends here.

<assembly Name="Vintasoft.Barcode" CoveredStatements="0" TotalStatements="0" CoveragePercent="0">
      
<Namespace Name=" Vintsoft barcode is an external library. So I added that to the excludes together with a few others. And on the next run everything works just fine. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/TeamCity6_dotcover_3.png" alt="" title="" width="740" height="297" />
</div>

## Conclusion

Dotcover support in TeamCity is awesome. Allthough I had at least 2 libraries it didn&#8217;t like, but those are external ones so i don&#8217;t care about coverage in them. But it was easy enough to exclude them. I submitted a report of this to the [JetBrains Teamcity discussion forum][1].</assembly>

 [1]: http://devnet.jetbrains.net/message/5280196#5280196