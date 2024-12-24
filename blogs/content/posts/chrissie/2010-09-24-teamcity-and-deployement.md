---
title: TeamCity and deployement
author: Christiaan Baes (chrissie1)
type: post
date: 2010-09-24T07:44:10+00:00
ID: 907
url: /index.php/desktopdev/mstech/teamcity-and-deployement/
views:
  - 2958
categories:
  - General Purpose Languages
  - Microsoft Technologies
tags:
  - deployement
  - teamcity

---
## Introduction

As you all know, I [moved to teamcity][1] for my nightly and continuous builds. Now I have also done the same for the deployment builds. My deployment builds are not yet continuous but I&#8217;m just 5 seconds away from them.

## Work flow

My work flow is very simple for deployment builds. 

1. Rebuild debug
  
2. run unittests on debug build
  
3. Rebuild release build
  
4. Copy to deployment folder and let the updater do it&#8217;s work.

As you can see items 1 and 2 are the same as a nightly build. So I would prefer to use the same configuration for that. Number 4 is a little complex but very easy to do in Finalbuilder. So I want ot run that script for it.

## TeamCity template

So I already have steps 1 and 2 defined somewhere, and all I need to do is use those. In Teamcity this means creating a template from the first one and inheriting the second one from this template. It&#8217;s all explained in the docs so you can read them. 

## Release build

For step 3 you only need to create a new configuration that only has the runner defined and that has a &#8220;Build dependency trigger&#8221; on the configuration of step 1 and 2. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Teamcity/Teamcity4.png" alt="" title="" width="721" height="421" />
</div>

## Finalbuilder script

For step 4 we use another configuration that also has a &#8220;Build dependency trigger&#8221; but this time on the configuration in step 3.
  
This configuration also only has the runner defined. Like this.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Teamcity/Teamcity5.png" alt="" title="" width="726" height="391" />
</div>

And as you can see this script has a few steps in it.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Teamcity/TeamCity6.png" alt="" title="" width="1153" height="460" />
</div>

## Conclusion

Now I just have to run Release software build and the other 2 steps will be run one after the other.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Teamcity/Teamcity7.png" alt="" title="" width="1022" height="257" />
</div>

To make this continuous I just have to add a VCS trigger to that configuration and it will be done. But for now I do not see a reason to that yet. 

<span class="MT_smaller">Only 5 more posts untill the 250th and final post.</span>

 [1]: /index.php/DesktopDev/GeneralPurposeLanguages/moving-to-teamcity