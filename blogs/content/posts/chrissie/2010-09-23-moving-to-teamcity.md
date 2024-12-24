---
title: Moving to TeamCity
author: Christiaan Baes (chrissie1)
type: post
date: 2010-09-23T10:50:46+00:00
ID: 905
url: /index.php/desktopdev/mstech/moving-to-teamcity/
views:
  - 4647
categories:
  - General Purpose Languages
  - Microsoft Technologies
tags:
  - finalbuilder server
  - teamcity

---
## Introduction

I have been using [Finalbuilder Server][1] for my continuous integration for over a year now and I was pretty happy with it. But I had a problem with some of my tests. For some reason my NUnit-View-tests. Cause a problem. I even know more or less what the problem is too, and it is an old third party control that I use and that I can&#8217;t just replace in a few minutes. So I either had to remove those tests or look for something else. And I knew that my tests ran under the Resharper UnitTestRunner, the Resharper Unittestrunner uses a homemade version of NUnit because of licensing isseus. And TeamCity just happens to use the same runner so I was hopefull that this would solve my problem.
  
And the problem is not that tests fails, the problem is that it just hangs when they are finished.

I have heard a lot of people using [TeamCity][2] and since it is more or less free. I decided to try that out.

## Install

Don&#8217;t be silly I won&#8217;t explain the install process, just start the installer and click next a few times and hope you reach the finish button when it is done without any problems.
  
Just to say that installation is painless. I installed the webserver on port 81 since port 80 was already reserved to Finalbuilder server which I was keeping around for a bit longer.

## Letting it build

Setting up a new process is easy in TeamCity. BUt everything is centered around CVS->Build->Unittest. Doing more asks a bit more work, in Finalbuilder server it was much eassier to add something to that Finalbuilder server afterall just runs your Finalbuilder projects and they can do lots of things. However you can also run Nant tasks or just straight Finalbuilder projects in Teamcity. But that didn&#8217;t seem like the way to do it at first.

First you start by creating a project and giving that a name.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Teamcity/Teamcity1.png" alt="" title="" width="970" height="707" />
</div>

Then you go and setup your CVS, in my case Git. My Git repository was the only thing I had trouble with to setup.
  
As you might know from my previous posts I use a share to store my central repository and I set it up with <code class="codespan">git --bare init</code> but I could not find anything in the documentation how to reference this in a Fetch URL. And I kept hitting a brick wall until by accident I found the yellow brick road.

I tried these without success:

```
file:///O:/SourceCode
file:///O:/SourceCode.git
file:///O:/SourceCode/.git
file:///servername/sharename/SourceCode
file:///servername/sharename/SourceCode.git
file:///servername/sharename/SourceCode/.git
o:/SourceCode
o:/SourceCode.git```
and many others

but this worked.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Teamcity/Teamcity2.png" alt="" title="" width="756" height="654" />
</div>

Then you setup the runner which is easy too, but it disturbs me that Targets, Configuration and platform are magic strings. This is the bit where Finalbuilder shines since it makes these kinds of things much easier to setup.

And here you also pick which tests to run.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Teamcity/Teamcity3.png" alt="" title="" width="704" height="504" />
</div>

As you can see it is very easy to setup. Apart form the Run tests from textbox. I have 10 projects that contain tests and this box those not make it easy to enter them or to maintain them. Much easier in Finalbuilder.

Then I set 2 triggers on it. One nightly and one when the CVS changes. Both triggers took no effort whatsoever to setup.

And that is how easy it is to setup continious integration with Teamcity.

## Conclusion

<span class="MT_red">I&#8217;m not unhappy with Finalbuilder server. And I&#8217;m not saying you should switch to Teamcity because it is better than Finalbuilder server. I&#8217;m just saying I did and that I found Teamcity to suit my needs.</span>

And yes my tests now run without a problem.

<span class="MT_smaller">Only 6 posts left until my 250th and final post.</span>

 [1]: http://www.finalbuilder.com/finalbuilder-server.aspx
 [2]: http://www.jetbrains.com/teamcity/