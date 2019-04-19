---
title: Continuous Delivery Project – Setting up Continuous Integration
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-12-15T11:06:00+00:00
ID: 1411
excerpt: A continuous integration server verifies that all of the currently committed changes play well together and reduces the elapsed time between a team member committing a change and finding out it leaves the build in a poor state. The faster we find out about a defect or unstable build, the fresher the changes are in our minds and the faster we can fix it.
url: /index.php/enterprisedev/application-lifecycle-management/continuous-delivery-project-setting-up/
views:
  - 21083
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
tags:
  - asp.net mvc
  - continuous delivery
  - continuous integration
  - jenkins
  - msbuild
  - mvc music store
  - webdeploy

---
A continuous integration server verifies that all of the currently committed changes play well together and reduces the elapsed time between a team member committing a change and finding out it leaves the build in a poor state. The faster we find out about a defect or unstable build, the fresher the changes are in our minds and the faster we can fix it.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Overview_p1.png" title="Delivery Pipeline - Focus of Current Post" /><br /> Delivery Pipeline – Focus of Current Post
</div>

This is the second post in a multi-part series on my Continuous Delivery pipeline project. The [first post][1] discussed Continuous Delivery, defined the process I am building, and outlined the technology selections I've made for the project. In this post I will begin setting up Continuous Integration for the project using Jenkins as a build server, MS Build to execute builds, and BitBucket to serve as the source code repository. 

## Server Setup

Prior to setting up the build server, I added a [repository on BitBucket][2] to serve as the central code repository, completed the [MVC Music Store tutorial][3] (full code available [on Codeplex][4]), and pushed the commits to the remote repository. 

There are three major differences between my version of the database and the one on MSDN:

  1. My copy uses a second sdf (SQL CE) database for authentication instead of SQL Express
  2. I'm using the Universal Providers for ASP.Net membership ([Install-Package System.Web.Providers][5])
  3. I have included the sdf files in the ASP.Net project (not something you would want to do in a production environment)

My server is a Windows 2008 R2 VM with 2GB of RAM assigned to it and a single 32GB harddrive. It was a clean, sysprepped image with no additional software installed.

## Installation

To get started on the new build server VM, I've installed the following software:

  * [Chrome][6] – because IE was annoying me
  * [UnxTools][7] – Extra tools Jenkins needs that mimic several Unix commands
  * <a href=http://jenkins-ci.org/"" title="Jenkins Downloads">Jenkins</a> – The installer will install the JRE and latest version of Jenkins
  * [Jenkins][8] – .Net Framework 4 (Check windows updates afterwards)
  * [Mercurial][9] – the windows version will install with tortoiseHg

With a couple reboots along the way, all the packages are installed with little extra effort.

## Jenkins Configuration

With the packages above in place, I can start up Jenkins and began configuring it. To start Jenkins, run <code class="codespan">java -jar "C:Program Files (x86)JenkinsJenkins.war"</code> and then point a browser to http://localhost:8080 to access the dashboard. There are also instructions to [set up Jenkins as a service][10].

_Note: Jenkins somehow magically set itself up as a service on my system (or I was really low on coffee when I was initially poking around it), so if you are following along on your own install, you may want to try accessing the dashboard prior to running the jar to see if it's already running._

<div style="text-align: center; font-size: .9em; color: #666666;">
  <a href="http://tiernok.com/LTDBlog/ContinuousDelivery/dashboard_lg.png" title="Larger picture" target="_blank"><img src="http://tiernok.com/LTDBlog/ContinuousDelivery/dashboard.png" title="Jenkins Dashboard" /></a><br /> Jenkins Dashboard (unfortunately a later shot as I misplaced some of my earlier screenshots)
</div>

The side menu offers a link to the server settings (Manage Jenkins), and from there I get a list of sub-menus in the main area that includes “Plugins”. To start with I'll install the plugins for Mercurial, Twitter, and MS Build from the “Available” tab on the plugins screen. After installing, system-wide options for the plugins are added in the system configuration screen (Manage Jenkins – Configure System). 

### Mercurial

The mercurial configuration is straightforward and offers a reasonable set of defaults, so of course I changed it. I added the path for the mercurial binaries to my PATH environment to make command-line access easier outside of the build server and then modified the mercurial configs in the build server to reflect that change.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <a href="http://tiernok.com/LTDBlog/ContinuousDelivery/config_mercurial_lg.png" title="Larger picture" target="_blank"><img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_mercurial.png" title="Jenkins Configuration - Mercurial" /></a><br /> Jenkins Configuration – Mercurial
</div>

My simplified configuration is the name of the executable and all blanks for the rest of the values.

### MS Build

The latest MS Build executable is installed as part of the .Net framework installation. In the Jenkins server setup, I add an MS Build item, naming it with it's version number (I can add separate, named configurations for each version later if I'm so inclined) and pointing the path to <code class="codespan">"C:WindowsMicrosoft.NETFrameworkv4.0.30319MSBuild.exe"</code>.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <a href="http://tiernok.com/LTDBlog/ContinuousDelivery/config_msbuild_lg.png" title="Larger picture" target="_blank"><img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_msbuild.png" title="Jenkins Configuration - MS Build, Framework 4" /></a><br /> Jenkins Configuration – MS Build, Framework 4
</div>

_Note: You can define multiple MS Build executables if you have projects that run on different versions. Naming them clearly will help when you later need to select the appropriate MS Build exe to build with_

### Twitter

As I pointed out in the first post, I decided I would use twitter for status notifications, as twitter is more widely accessible and won't clog up my inbox (the downside being limited status information). There is important additional information on the [plugin page][11] for setting it up.

## Setting up the CI Job

With the server configured, I can move on to setup the initial CI build job. Initially, this job will be responsible for picking up changes from mercurial, executing the build, and reporting the results.

  1. Select “New Job” from top left menu
  2. Select “Build a free-style software project”
  3. Enter a Name
  4. Enter Details 
      1. Select Mercurial for SCM and enter URL for the repository (I am using bitbucket for this example) as well as selecting repository browser (bitbucket)
      2. Initially I'll leave build triggers not defined
      3. Configure MS Build by specifying the <code class="codespan">*.sln</code> 
      4. check the “twitter” checkbox at bottom
      5. Run build by clicking “Build Now” at top left
  5. Start debugging build problems

_Note: Though I didn't show it here, there is also an advanced option under the mecurial settings called “Clean Build”. This will clean the workspace before each build so binaries and test results won't pollute later builds_

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_dashboard_failed.png" title="Jenkins Configuration - Failed Build on Dashboard" /><br /> Dashboard View of Failed Build
</div>

My first attempted build fails. The details are available by opening the build and clicking the Console Log link in the left menu (which changes to reflect the context of the screen we are on). The console log displays the raw output of the commands executed during the build.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_failedbuild.png" title="Jenkins Configuration - Failed Build Console Log" /><br /> Looking at the Console Log for a Failed Build
</div>

Here are the errors I had to work through in order to get the initial build to work. Some of them were me missing feedback from the system or incorrect configurations.

  1. Error (twice), console log told me hg wasn't recognized 
      * hg hadn't actually installed the first time due to windows updates being in middle of another install
      * I rebooted to finish windows update, installed tortoisehg, rebooted to have clean startup (and paths), and the issue was corrected
  2. Failure – In the console log it complained about not being able to find the MS Build executable 
      * Returned to project settings and switched MS Build option from (default) to the one I had configured above in global settings
  3. Error MSB4019: The imported project “C: ... Microsoft.WebApplication.targets” was not found 
      * Options: 
          * Install VS 2010 Shell (http://www.microsoft.com/download/en/details.aspx?id=115)
          * Install Visual Studio
          * Copy folder from existing install (C:Program FilesMSBuildMicrosoftVisualStudiov10.0WebApplications)
      * I went with option 1 and ran windows updates again before continuing
  4. error CS0234: The type or namespace name 'Mvc' does not exist 
      * Would have been fixed if I had installed VS (oh well)
      * Download and install MVC: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=4211

At this point the initial build runs successfully. 

### Refining the Setup

After getting the initial build setup, it's time to add some refinements. First up is switching the build to run in release mode by adding <code class="codespan">"/p:Configuration=Release"</code> to the command line arguments in the MS Build section.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_buildchanges_rel.png" title="Jenkins Configuration - Adding Release to Build Args" /><br /> Jenkins Configuration – Adding Release to Build Args
</div>

Now that I have it working, I also want to add the option to automatically run when new changes are committed to source control. The Build Triggers section of the job configuration controls how jobs are triggered, so I'll select the “Poll SCM” option to poll my source control repository. A value of <code class="codespan">"*/5 * * * *"</code> will set it to check every 5 minutes (which may be overkill given how few updates I will be making over the course of this project, but oh well).

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_buildchanges_poll.png" title="Jenkins Configuration - Polling for Build Trigger" /><br /> Jenkins Configuration – Defining Polling for Build Trigger
</div>

_Note: Timing uses Unix cron-style values. Basically the string is used as a test against the current time to see if a particular step is to be run, so 5 \* \* \* \* would run only if the minutes value was a 5, while \*/5 \* \* \* * runs if it is divisible by 5._

## Capturing the Results

The last step of the build stage is to capture the resulting binaries and website pages so they can be deployed consistently to other environments. The addition of WebDeploy to Visual Studio and IIS has made web deployment easy* to manage, which will simplify getting an archive of the results and my deployment scripts later.

_Note: \***Web Deploy has made this really easy IN THEORY. This is the topic of a later post in the series and is also the reason screenshots and check-ins for the early stages may reflect dates in early November and these posts are being written in December._

By default, when I create a deployment package I will get a folder of all the cshtml, dll, and so on files I need to run the site. In the project properties for the website, there is a build option to zip these files as a package after building it, which will simplify archival even further.

_Project properties, select the tab for “Package/Publish Web” and check the “Create deployment package as zip” option_

The last piece is to tell MSBuild I want to build the web deployment package. In the MSBuild step of the CI job, I add the command-line flag of <code class="codespan">"/p:DeployOnBuild=True"</code>, which will be passed on to the individual projects in the solution to act on if they understand it (which the web project will and the unit test project will not, handy).

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_buildchanges.png" title="Jenkins Configuration - Failed Build Console Log" /><br /> Looking at the Console Log for a Failed Build
</div>

At this point running another build fails, with multiple errors complaining about Package steps (like CheckAndCleanMSDeployPackageIfNeeded) failing. The solution is to install the WebDeploy 2.0 refresh package on the server, located [here][12]. Once this is installed, the build is able to complete successfully.

Now that I have a nice, tidy package of the deployable build, I need to put it somewhere for longer term use. In the Post-Build Actions of my job configuration, there is an option to archive artifacts from the build. Checking this box and entering the path for the zip file from the Project Properties screen (objDebugPackageMvcMusicStore.zip) tells Jenkins to archive that zip file as the artifacts from each build.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <a href="http://tiernok.com/LTDBlog/ContinuousDelivery/config_successfulbuild_lg.png" title="Larger picture" target="_blank"><img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_successfulbuild.png" title="Jenkins Configuration - Successful Build" /></a><br /> Jenkins Configuration – Successful Build
</div>

After executing another successful build, we can see the build server has archived the zip file (above). If I click that zip file I'll be prompted to download it to a local machine.

## Test the Package

We're not done until we test the package. Luckily testing a WebDeploy package is pretty easy, all we have to do is open the IIS configuration screen, select the default web site, and then use the import button on the right side of the interface to import the zip file. This imports all of the files, sets up the application, and gives me a running website. There is more information on WebDeploy in [this post by ScottGu][13] and we will get more in depth with it in later steps.

_Note: It's interesting to note that this is where I found the first bug in MVC Music Store. The images in the CSS file were defined assuming the application was at the root level ([fix][14]), as were the navigation paths in the header ([fix][15])._

## Next Steps

We now have a working Continuous Integration stage that will detect checked in changes, build them, and create a deploy package. The next step is to execute and capture the results for the unit tests, however before capturing the results we need to have unit tests, and to have unit test we have to make the Music Store tutorial code testable. The next post will cover that conversion. It's interesting to note, especially if you are one of those people that believe unit tests to be wasteful, that the very first controller I put under test in this very public, very widely deployed, open source project contains a defect.

<ul class="thelist">
  <li>
    <a href="http://wiki.ltd.local/index.php/Eli%27s_Continuous_Delivery_Project" title="Wiki post for Eli's Continuous Delivery Project">Wiki Post</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/starting-a-continuous-delivery-project" title="Starting a Continuous Delivery Project">Starting a Continuous Delivery Project</a>
  </li>
  <li class="cur">
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

 [1]: /index.php/EnterpriseDev/starting-a-continuous-delivery-project "Starting the Continuous Delivery project"
 [2]: https://bitbucket.org/tarwn/mvcmusicstore.main "Source repository on BitBucket"
 [3]: http://www.asp.net/mvc/tutorials/mvc-music-store "MVC Music Store Tutorial on ASP.Net site"
 [4]: http://mvcmusicstore.codeplex.com/ "See tutorial on CodePlex"
 [5]: http://nuget.org/List/Packages/System.Web.Providers "Universal Providers on Nuget"
 [6]: http://www.google.com/chrome "Chrome install link"
 [7]: http://sourceforge.net/projects/unxutils/ "Install Unxtools"
 [8]: http://www.microsoft.com/download/en/details.aspx?id=17851 ".Net Framework 4 Downloads"
 [9]: http://mercurial.selenic.com/ "Mercurial Download"
 [10]: https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+as+a+Windows+service ""
 [11]: https://wiki.jenkins-ci.org/display/JENKINS/Twitter+Plugin "Jenkins Twitter Plugin"
 [12]: http://blogs.iis.net/msdeploy/archive/2011/04/05/announcing-web-deploy-2-0-refresh.aspx "WebDeploy 2.0 Refresh"
 [13]: http://weblogs.asp.net/scottgu/archive/2010/09/13/automating-deployment-with-microsoft-web-deploy.aspx "Automating Deployment with Microsoft Web Deploy"
 [14]: https://bitbucket.org/tarwn/practicerepo/changeset/87b38e362428 "Changeset for the fix"
 [15]: https://bitbucket.org/tarwn/practicerepo/changeset/843bc10fdd7c "Changeset for the fix"