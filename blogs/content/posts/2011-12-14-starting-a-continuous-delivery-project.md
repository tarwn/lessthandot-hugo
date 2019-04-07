---
title: Starting a Continuous Delivery Project
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-12-14T10:43:00+00:00
ID: 1410
excerpt: "I find that often the hardest part of trying a new technology or principle is finding a project that is simple enough to work on in my spare time, yet complex enough to be useful. Several weeks ago I came up with the idea to use a common project to serve as a platform for additional projects and experiments. The first project, build an automated pipeline that will verify the project remains stable (or notify me when it isn't) throughout its lifetime."
url: /index.php/enterprisedev/application-lifecycle-management/starting-a-continuous-delivery-project/
views:
  - 13216
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
tags:
  - asp.net mvc
  - continuous delivery
  - continuous integration
  - mvc music store
  - unit testing

---
I find that often the hardest part of trying a new technology or principle is finding a project that is simple enough to work on in my spare time, yet complex enough to be useful. Several weeks ago I came up with the idea to use a common project to serve as a platform for additional projects and experiments. The first project, build an automated pipeline that will verify the project remains stable (or notify me when it isn&#8217;t) throughout its lifetime.

## Why a Continuous Delivery Model?

Continuous Delivery focuses on standardizing the environments and processes for product delivery, with an aim to create a clear and consistent process from committing new code to having a deliverable product. Creating a consistent process reduces variability and risks involved with manual deployment, ensures all &#8220;ready to be released&#8221; products meet our build and testing standards, creates a faster feedback loop so that problems are detected sooner (and thus can be fixed cheaper), and adds a level of auditability that rarely exists with manual deployment processes. 

There is a good article [on InformIT][1] by Jez Humble (who also coauthored the book [Continuous Delivery][2]) that covers the benefits more in depth.

So why use a continuous delivery model for my home lab? 

When I spend a weekend playing with caching in my ASP.Net project, I want to be able to walk away from the project knowing it still works and I won&#8217;t be spending my next Saturday trying to figure what I did to break my test systems. This project will also provide a future testbed for load testing and static analysis tools. 

Plus seeing all the green &#8220;pass&#8221; lights is nice 🙂

## Designing the Process

I already know several of the components I am going to use. In some cases I have purposefully decided to use technologies I am not familiar with. I am going to be enforcing unit and acceptance tests, potentially adding static analysis tools, and publishing the results to the world via public code repositories, this blog, and a wiki entry.

_One of the challenges is that all work on this project, including background research, will be in my spare time. My current work environment doesn&#8217;t include a CI system and some of the technologies are new to me. In addition, this project will also be competing with new computer games that come out and my 9 month old son. So it may take more then a few weekends._

### The Project

Prior to designing the deployment pipeline, I selected the project that will serve as the ongoing guinea pig. The ASP.Net MVC3 Music Store tutorial project offered an opportunity to work more with ASP.Net MVC and Entity Framework Code-First as well as serve as a future platform to play with adaptive web design techniques, HTML 5, various output and data caching methods, data layer implementations or NoSQL back-ends, and I&#8217;m sure many more ideas I have yet to consider. It&#8217;s both big enough to have a variety of use cases but small enough that I can finish future projects in a weekend or two.

### The Pipeline

The delivery pipeline will look something like this:

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Overview.png" title="Pipeline overview diagram" /><br /> Overview of the delivery pipeline
</div>

Initially there won&#8217;t be any configuration management or database change management, and the test coverage will be less than complete. These are all follow-up projects I can take on once I get the main pipeline working.

### Technology Selection

There are (or were) a number of technology decisions needed to get started. This is the line-up:

ASP.Net MVC 3
:   Development stage, to get more practice with MVC

Entity Framework
:   Development stage, I don&#8217;t really care for entity framework, so I&#8217;m trying to use it more

MS Test
:   Development stage, I like MS Test for the dev stage because of it&#8217;s integration into Visual Studio

Mercurial
:   Source Control, local and BitBucket

Jenkins
:   Jenkins, I&#8217;d heard good things about it, lots of plugins, I&#8217;d only ever used TFS in the past for CI and Chrissie already has posts on TeamCity 🙂

MS Build
:   CI Stage, MS Build to build the code, transform configurations, and create the deployment package

MS Test
:   CI Stage, MS Test standalone executable to run the MS Test unit tests

IIS 7
:   Deploy Steps, IIS 7 supports the new webdeploy capabilities, which will make deployment much easier

MS Deploy
:   Deploy Steps, I haven&#8217;t had an opportunity to do more than push the &#8220;Deploy&#8221; button in WebMatrix, looking forward to getting more in depth with WebDeploy

VBScript
:   Deploy Steps, A small vbscript capable of using XMLHTTP to make raw HTTP GET requests (potentially switch to PowerShell later)

Nunit
:   Automated Test Stage, Platform and testrunner for the automated interface tests

Selenium WebDriver
:   Automated Test Stage, Automated interface testing to be driven by the Nunit framework

Build Pipeline Plugin
:   Dashboard, There is a build pipeline plugin for Jenkins that I intend to try out

Twitter
:   Communications, I&#8217;ll be using twitter for status notifications at each stage

There are also a number of other plugins for Jenkins which I&#8217;ll mention at the appropriate steps.

## Next Steps

That&#8217;s the setup. I&#8217;ve created [a forum post][3] to discuss the whole process (although comments on individual blogs are obviously still welcome). I also have created [a wiki page][4] to tie all the posts together and give a current status for the project. You can also watch me break things by following [@TarwnBuildSrvr][5] (currently pretty dull output, may be another project there). I&#8217;ll also post the &#8216;production&#8217; URL for the project and URLs for the source code on bitbucket.

<ul class="thelist">
  <li>
    <a href="http://wiki.ltd.local/index.php/Eli%27s_Continuous_Delivery_Project" title="Wiki post for Eli's Continuous Delivery Project">Wiki Post</a>
  </li>
  <li class="cur">
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/starting-a-continuous-delivery-project" title="Starting a Continuous Delivery Project">Starting a Continuous Delivery Project</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-project-setting-up" title="Setting up the Continuous Integration stage">Setting up the Continuous Integration stage</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/UnitTest/continuous-delivery-project-making-mvcmusicstore" title="Making ASP.Net MVC Music Store Testable">Making ASP.Net MVC Music Store Testable</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/UnitTest/continuous-delivery-project-incorporating-the" title="Incorporating Unit Tests in the CI stage">Incorporating Unit Tests in the CI stage</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-project-deploy-and" title="Deploy and Smoke Test">Deploy and Smoke Test</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-adding-an-automated" title="Adding an Automated Interface Testing stage">Adding an Automated Interface Testing stage</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-dashboard-qa-and" title="Dashboard, QA and Production Deployment stage">Dashboard, QA and Production Deployment stage</a>
  </li>
</ul>

 [1]: http://www.informit.com/articles/article.aspx?p=1641923 "Continuous Delivery: The Value Proposition"
 [2]: http://www.amazon.com/Continuous-Delivery-Deployment-Automation-Addison-Wesley/dp/0321601912/ "Continuous Delivery at Amazon"
 [3]: http://forum.ltd.local/viewtopic.php?f=121&t=15760 "Forum post for discussion"
 [4]: http://wiki.ltd.local/index.php/Eli%27s_Continuous_Delivery_Project "Wiki post for Eli's Continuous Delivery Project"
 [5]: http://twitter.com/TarwnBuildSrvr "Eli's Build Server on Twitter"