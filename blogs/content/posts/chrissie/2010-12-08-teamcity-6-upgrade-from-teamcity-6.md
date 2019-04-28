---
title: Teamcity 6 upgrade from teamcity 5
author: Christiaan Baes (chrissie1)
type: post
date: 2010-12-08T12:06:58+00:00
ID: 967
excerpt: |
  Introduction
  
  Teamcity 6 came out and I upgraded. TeamCity is free so I see no reason why not to use it. And if there is a new version we just have to have it like woman need new shoes. Here is my adventure with the upgrade.
  
  Install
  
  Just downloa&hellip;
url: /index.php/itprofessionals/softwareandconfigmgmt/teamcity-6-upgrade-from-teamcity-6/
views:
  - 3310
categories:
  - Software and Configuration Management
tags:
  - teamcity

---
## Introduction

[Teamcity][1] 6 came out and I upgraded. TeamCity is free so I see no reason why not to use it. And if there is a new version we just have to have it like woman need new shoes. Here is my adventure with the upgrade.

## Install

Just download it and click install, then click next a few times and finish.

And all worked pretty much from the first go.

## Problem

Except (as always) one thing did not work.

I got this error on my VCS root.

> &#8216;git fetch&#8217; command failed.
      
> stderr: Exception in thread &#8220;main&#8221; jetbrains.buildServer.vcs.VcsException: Cannot access repository //texsrv2/Sharetex/Sourcecode. If TeamCity is ran as a service, it cannot access network mapped drives. Make sure this is not your case.
      
> at jetbrains.buildServer.buildTriggers.vcs.git.GitVcsSupport.checkUrl(GitVcsSupport.java:1440)
      
> at jetbrains.buildServer.buildTriggers.vcs.git.GitVcsSupport.openTransport(GitVcsSupport.java:1407)
      
> at jetbrains.buildServer.buildTriggers.vcs.git.Fetcher.fetch(Fetcher.java:64)
      
> at jetbrains.buildServer.buildTriggers.vcs.git.Fetcher.main(Fetcher.java:43)

And here it is in technicolor.

<div class="image_block">
  <img src="/wp-content/uploads/users/chrissie1/test/Teamcity6_1.png" alt="" title="" width="594" height="94" />
</div>

And

<div class="image_block">
  <img src="/wp-content/uploads/users/chrissie1/test/Teamcity6_2.png" alt="" title="" width="657" height="235" />
</div>

[This happens to be a known issue][2].

> Teamcity ran as a Windows service cannot access a network mapped drives, so you cannot work with git repositories located on such drives. To make this work run TeamCity using teamcity-server.bat.

But it isn&#8217;t very clear how to solve it.

It is missing a few steps.

First you stop the teamcity services.

<div class="image_block">
  <img src="/wp-content/uploads/users/chrissie1/test/Teamcity6_3.png" alt="" title="" width="456" height="53" />
</div>

The 2 above, namely Teamcity Build Agent and Teamcity Web Server.

Then you go to the teamcity bin folder. and execute the teamcity-server.bat start thing.

<div class="image_block">
  <img src="/wp-content/uploads/users/chrissie1/test/Teamcity6_4.png" alt="" title="" width="804" height="634" />
</div>

And then all is well.

Or so I thought. The service Teamcity Build Agent was not to be closed. SO I restarted it and the teamcity just went belly up and the I had to run the bat file again.

And then all was well.

The end.

## Conclussion

Uhm, don&#8217;t upgrade on an empty stomach. Did I mention Temacity is free? Just to annoy Howard I misspelled Conclusion ;-).

 [1]: http://www.jetbrains.com/teamcity/
 [2]: http://confluence.jetbrains.net/display/TCD6/Git+%28JetBrains%29