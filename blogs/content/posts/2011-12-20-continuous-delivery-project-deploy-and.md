---
title: Continuous Delivery Project – Deploy and Smoke Test
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-12-20T10:56:00+00:00
ID: 1449
excerpt: "Executing an integration build and unit test run on the build server is all well and good, but before we can say a build is complete and ready to go, it's a good idea to know it will work when it is deployed. Executing a test deployment and then smoke testing it will ensure the archived build is ready to be deployed to test or production environments and that the necessary configurations and resources are available and working."
url: /index.php/enterprisedev/application-lifecycle-management/continuous-delivery-project-deploy-and/
views:
  - 18859
rp4wp_auto_linked:
  - 1
categories:
  - Application Lifecycle Management
tags:
  - continuous delivery
  - continuous integration
  - jenkins
  - mvc music store
  - webdeploy

---
Executing an integration build and unit test run on the build server is all well and good, but before we can say a build is complete and ready to go, it's a good idea to know it will work when it is deployed. Executing a test deployment and then smoke testing it will ensure the archived build is ready to be deployed to test or production environments and that the necessary configurations and resources are available and working.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/Overview_p4.png" title="Delivery Pipeline - Focus of Current Post" /><br /> Delivery Pipeline &#8211; Focus of Current Post
</div>

This is the fifth post in a multi-part series on my Continuous Deployment pipeline project. The [previous post][1] covered integrating unit tests into the Continuous Integration stage. This post will cover the final portion of that stage, an automated deployment of the site and smoke test to make sure it can be deployed in a working state.

## Preparing to Deploy

The first thing we need in order to deploy is a web server. I initially intended to use a local IIS Express instance to do the test deployment but after some very long hours I decided to stop trying to make MSDeploy do something it obviously did not want to do. When the IIS Express idea fell through, I decided to use separate Applications on my newly created AVL-BETA-01 VM for different stages in my build pipeline, with a dedicated Application target just for this brief deploy-and-test step.

### Server Details:

  * Windows 2008 R2 VM, Patched and up to date
  * Installed Web Deploy 2.1 through Web Platform Installer (http://www.microsoft.com/web)
  * Added Web Server/IIS role to the server
  * In the Role Services for IIS, add the non-default Management Service

Web Management service is a required service for web deploy, as it is the endpoint that exposes remote configuration capabilities to clients like WebDeploy. 

  * Created a build server account (buildsrvr) and set it up as a local admin (cheating), made it the runas acct for Web Management Service
  * Enabled Remote: Open IIS Manager, Click Management Service, Click enable remote, Apply

Using [this guide][2], I followed the steps to configure the web deployment handler. After several hours of wrestling with less-than-informative 401 permissions errors, even when doing local manual tests, I finally gave in and used the server's local administrator account for my webdeploy scripts.

_I'm very bitter about this 401 deal. There is a huge list of issues that can potentially happen and they all boiled down to just a few error messages. This whole matter would have been much easier if they had either (a) output detailed errors to the server event log, (b) given me the option on the server to put it into a promiscuous error mode temporarily to output more useful errors to the client, or (c) something in between. Offering the same exact message for multiple issues meant I had no idea when I was making changes whether I was fixing one of several problems or just making changes that had no effect._

The last step was to modify my target application pool to use .Net framework 4.0 (I cheated and used the default pool).

## Manually Deploying

With the server setup, the last few steps were completed by trying to manually deploy the site to the target server. Opening a command prompt, I executed a variant of the following command to attempt to deploy my site package to the server:

<code class="codespan">"C:Program FilesIISMicrosoft Web Deploy V2\msdeploy.exe" -source:package='c:Program Files (x86)JenkinsjobsASPNet MVC Music Store CI BuildworkspaceMvcMusicStoreobjReleasePackageMvcMusicStore.zip' -dest:auto,computerName='AVL-BETA-01',userName='AVL-BETA-01Administrator',password='MYPASSWORD',includeAcls='False' -verb:sync -disableLink:AppPoolExtension -disableLink:ContentExtension -disableLink:CertificateExtension -setParam:"IIS Web Application Name"="Default Web Site/MvcMusicStore_SmokeTest" -whatif</code>

This command basically says that I want to deploy from the packaged zip file to the following destination, with the additional “IIS Web Application Name” parameters set to the name of the website and Application I want to deploy to on the server. The “-whatif” flag on the end tells MSDeploy that I want to do a simulated deployment. This causes MSDeploy to do all of the steps of doing a deployment short of actually copying the files up to the server. This was useful during the troubleshooting and configuration above.

## Deploying

Removing the whatif flag, I can now try to do a full deployment. Unfortunately the site does not run, replying with an ASP.Net error screen that points out missing helper references. Following the helpful instructions [on this site][3], I'll modify my project properties to include the necessary libraries. After a fresh build and second manual deployment, the site loads correctly.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <a href="http://tiernok.com/LTDBlog/ContinuousDelivery/config_deploy.png" title="Larger picture" target="_blank"><img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_deploy.png" title="Deployed to SmokeTest Location" /></a><br /> Deployed to SmokeTest Location
</div>

With the manual command successfully deploying, it's now a simple matter to add a new step to my job to execute this same command on every build.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <a href="http://tiernok.com/LTDBlog/ContinuousDelivery/firstdeploy.png" title="Larger picture" target="_blank"><img src="http://tiernok.com/LTDBlog/ContinuousDelivery/firstdeploy.png" title="Deployed Site" /></a><br /> Deployed Site
</div>

To catch us up to date, the build is now getting latest from the code repository, building it, running the unit test suite, and successfully deploying it to IIS on a test server.

## Smoke Testing the Deployment

Given how small this site is, the are only a few things I want to test on my deployed site to consider it a success. I'll want to know that the site is available, that I deployed the version I just finished building, and that some data from the database is being displayed properly.

Looking through those requirements, I need three things:

  1. A way to tell what version a deployed site is
  2. A script to execute an HTTP Request against the site
  3. A way to produce results my build engine understands

### Version Numbers on the Site

Adding a version number to the site is not too difficult. I'll add a text file to my application that will be used to store the version number, along with some logic for the application begins to read that version number and store it.

**MVCMusicStore/Content/buildVersion.txt**

```text
DEV
```
**MVCMusicStore/Global.asax**

```csharp
protected void Application_Start() {
	// ...
	StoreBuildVersion();
}

protected void StoreBuildVersion() {
	FileInfo fnfo = new FileInfo(Server.MapPath("~/Content/Buildversion.txt"));
	if (!fnfo.Exists) {
		Application["BuildVersion"] = "Unknown(1)";
	}
	else {
		try {
			using (StreamReader sr = fnfo.OpenText()) {
				Application["BuildVersion"] = sr.ReadToEnd();
			}
		}
		catch {
			Application["BuildVersion"] = "Unknown(2)";
		}
		
	}
}
```
With the version number stored in my application, I can output that value in the footer of each page by adding it to the main _Layout file that is used throughout the site.

**MVCMusicStore/Views/Shared/_Layout.cshtml**

```csharp
...
<div id="footer">
	<span class="version">Version: @HttpContext.Current.Application["BuildVersion"]</span>
	built with <a href="http://asp.net/mvc">ASP.NET MVC 3</a>
</div>
...
```
With the application will displaying the value from the BuildVersion file, all that is left is to insert a step in my job to write the current build number to the file before the project is built and packaged up.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <a href="http://tiernok.com/LTDBlog/ContinuousDelivery/config_buildnumber.png" title="Larger picture" target="_blank"><img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_buildnumber.png" title="Adding Build Number to Site" /></a><br /> Adding Build Number to Site
</div>

Ad the build number isadded as part of the CI build job, I'll see the word “DEV” in my development environment and the appropriate build number everywhere else. Now I can automate tests to check for the expected build number and trace deployed sites back to their original build history.

### Smoke Testing Script

Writing a script to test the deployed site is fairly straightforward and can be done in any of a dozen different languages. In order to fit my requirements above I need to be able to pass it a URL of the deployed site and a version number, and receive back some information to indicate whether my three test scenarios pass or fail. Because Jenkins has a built-in capability to parse Junit result files, I decided to add a third argument to indicate a test result file location and output the smoke test results as a file in Junit format.

I wrote my smoke test in VBScript and I don't currently have it in a public repository, but I can make it available if anyone is curious (it's not terribly complicated). The workflow of the script is:

  * Parse the arguments
  * Perform an HTTP GET on the specified URL
  * Test 1: Check if the HTTP status code was a 200
  * Read the return content of the page as string
  * Test 2: Execute a regular expression test to verify the version number matches the one passed in as an arg
  * Test 3: Execute a regular expression test to verify some genre links were generated from the database
  * Output a Junit-compatible file to the location specified as an argument

A recent sample of the output looks like this:
  
**SmokeTestResults.xml**

```xml
<testsuite classname="SmokeTests.BasicTests" failures="0" name="SmokeTests.BasicTests" skipped="0" tests="3" time="40.5312">
	<testcase name="BasicGet" time="40.4375"></testcase>
	<testcase name="VersionStamp" time="0.0625"></testcase>
	<testcase name="Genre Content" time="0"></testcase>
</testsuite>
```
With the script built, all I need to do is add a final “Windows Batch Command” step to my job to execute this script.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <a href="http://tiernok.com/LTDBlog/ContinuousDelivery/config_smoketest.png" title="Larger picture" target="_blank"><img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_smoketest.png" title="Job Configuration - Smoke Test" /></a><br /> Job Configuration &#8211; Smoke Test
</div>

And then configure the post-build step to import the result file as a Junit test result file.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <a href="http://tiernok.com/LTDBlog/ContinuousDelivery/config_smoketestresult.png" title="Larger picture" target="_blank"><img src="http://tiernok.com/LTDBlog/ContinuousDelivery/config_smoketestresult.png" title="Job Configuration - Smoke Test Results" /></a><br /> Job Configuration &#8211; Smoke Test Results
</div>

With this method I not only get the tests executed as part of every build, but the results are nicely aggregated with the unit test results.

<div style="text-align: center; font-size: .9em; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/ContinuousDelivery/alltests.png" title="All Tests for CI Build" /><br /> All Tests for CI Build
</div>

## Next Steps

With the Continuous Integration stage of the delivery pipeline built, it's time to start focusing on the later steps of the process. The next post will introduce an automated interface job that picks up where the Continuous Integration stage leaves off to execute a set of Selenium + Nunit tests against a freshly deployed copy of the website.

<ul class="thelist">
  <li>
    <a href="http://wiki.ltd.local/index.php/Eli%27s_Continuous_Delivery_Project" title="Wiki post for Eli's Continuous Delivery Project">Wiki Post</a>
  </li>
  <li>
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
  <li class="cur">
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-project-deploy-and" title="Deploy and Smoke Test">Deploy and Smoke Test</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-adding-an-automated" title="Adding an Automated Interface Testing stage">Adding an Automated Interface Testing stage</a>
  </li>
  <li>
    <a href="/index.php/EnterpriseDev/application-lifecycle-management/continuous-delivery-dashboard-qa-and" title="Dashboard, QA and Production Deployment stage">Dashboard, QA and Production Deployment stage</a>
  </li>
</ul>

 [1]: /index.php/EnterpriseDev/UnitTest/continuous-delivery-project-incorporating-the "Continuous Delivery Project - Incorporating the Unit Tests"
 [2]: http://learn.iis.net/page.aspx/516/configure-the-web-deployment-handler/ "Configure Web Deployment Handler"
 [3]: http://mr.chriscompton.me/2011/11/assembly-errors/ "Helper assembly errors"