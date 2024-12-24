---
title: Don’t forget to clean up your teamcity mess
author: Christiaan Baes (chrissie1)
type: post
date: 2011-07-25T09:31:00+00:00
ID: 1277
excerpt: |
  Introduction
  
  I've been using teamcity for a while now and just used it on a VM with limited diskspace. 40GB for a windows 2008 R2 server isn't all that much. But it still has a couple of Gigabytes left over so why should I worry... Yeah right. Today&hellip;
url: /index.php/itprofessionals/itprocesses/don-t-forget-to-clean/
views:
  - 5601
categories:
  - IT Processes
tags:
  - teamcity

---
## Introduction

I&#8217;ve been using teamcity for a while now and just used it on a VM with limited diskspace. 40GB for a windows 2008 R2 server isn&#8217;t all that much. But it still has a couple of Gigabytes left over so why should I worry&#8230; Yeah right. Today I got this message from teamcity.

> Warning: insufficient space on disk where the following directory resides c:/users/&#8230; Disk space available: 1009.32Mb; Please contact your system administrator.

## The solution

I use Teamcity 6.5.

So I contacted myself and looked where all my diskspace went. And I found that I had a lot of these.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/teamcity1.png?mtime=1311593043"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/teamcity1.png?mtime=1311593043" width="817" height="341" /></a>
</div>

And those things add up.

Now why do I have so many of them?

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/teamcity2.png?mtime=1311593155"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/teamcity2.png?mtime=1311593155" width="809" height="472" /></a>
</div>

Yep that&#8217;s right, teamcity holds on to everything forever. But we can change that for the big offenders. We do this by editing the cleanup rules.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/teamcity2.png?mtime=1311593155"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/teamcity2.png?mtime=1311593155" width="809" height="472" /></a>
</div>

You can find them in Administration > Build History Clean-up.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/teamcity3.png?mtime=1311593332"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/TeamCity/teamcity3.png?mtime=1311593332" width="466" height="669" /></a>
</div>

## Conclusion

I&#8217;m glad teamcity warned me of this and I&#8217;m glad it was simple to fix.