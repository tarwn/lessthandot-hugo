---
title: Teamcity and the Nunit build step
author: Christiaan Baes (chrissie1)
type: post
date: 2013-07-12T07:22:00+00:00
ID: 2135
excerpt: "Don't count on nunit giving you an exit code of 0 if the tests fail."
url: /index.php/architect/integrationarchitecture/teamcity-and-the-nunit-build/
views:
  - 6700
categories:
  - Information and Integration Architecture
tags:
  - nunit
  - teamcity

---
My goal for this build was to build the app, run unit test, make a nuget package of one of the projects and then publish the nuget package somewhere that makes sense.

I made the misstake of just using the defaults for teamcity and the default is to execute the next step only if the previous step finishes succesfully, meaning you get an exit code 0.

I even made a nice diagram for it.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/TeamCity/Teamcitybuild1.png?mtime=1373613070"><img alt="" src="/wp-content/uploads/users/chrissie1/TeamCity/Teamcitybuild1.png?mtime=1373613070" width="2354" height="3059" /></a>
</div>

See how it says Zero exit code and tests pass after the nunit step? 

The two are totally different things. Even if some tests fail the exit code can and most likely will still be zero. 

So if we leave this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/TeamCity/Teamcitybuild2.png?mtime=1373613305"><img alt="" src="/wp-content/uploads/users/chrissie1/TeamCity/Teamcitybuild2.png?mtime=1373613305" width="664" height="74" /></a>
</div>

And get failing tests, our nuget package will still be created. Because the exit code is 0.

It is better to set this in step 3 (build the nuget package)

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/TeamCity/Teamcitybuild3.png?mtime=1373613492"><img alt="" src="/wp-content/uploads/users/chrissie1/TeamCity/Teamcitybuild3.png?mtime=1373613492" width="642" height="60" /></a>
</div>

It will then not make a nuget package when the tests fail or the exit code is 0.

You can leave step 4 the default since the artifact won&#8217;t be created in step 3 and their will be nothing to publish anyway. 

I am using teamcity 8.0, nunit 2.6 and nuget 2.6.1.