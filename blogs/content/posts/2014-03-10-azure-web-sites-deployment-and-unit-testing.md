---
title: Azure Web Sites Deployment and Unit Testing
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-03-10T12:18:26+00:00
url: /index.php/enterprisedev/cloud/azure/azure-web-sites-deployment-and-unit-testing/
views:
  - 18266
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - azure
  - cassinidev
  - nunit
  - selenium

---
The automatic deployment option of Azure Web Sites is really cool and demos well. A few months ago I was curious how far I could push it, whether I could replace more complex projects that I currently deploy from my build server. I had read a couple posts on unit testing during deployment, but so far had not seen anyone take it all the way to interface testing. With tools like CassiniDev, Phantom, and Selenium, this seemed like a real possibility.

So the goal was to create an Azure Website, get the automated deployment working automatically when I commit, run Nunit tests with the Nunit test runner (not the VS runner), and then wire in UI testing with a combination of CassiniDev for hosting the site, Phantom as the browser, and Selenium as the magic that drives the two. 

This post covers the parts that worked, creating the Azure Web Site, settings up automated depoyment from the git repository, and customizing the deployment process to run the Nunit test runner, failing the build when a test fails.

## From the Beginning

This project started from an empty folder, the goal being to see how far I could push it until I ran into problems.

The first step was to create a github repository for the project and build the basic MVC4 project. I built just a basic MVC4 project with some text on a single page, just enough to show if it was working or not.

The source code is on github: [https://github.com/tarwn/CloudPixiesAndGhosts][1]

## Azure Website

Once I had a basic &#8220;hello World&#8221; page and a github repository, it was time to create the Azure Website that would be the deployment target.

<div id="attachment_2373" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/01_CreateSiteStep1.png"><img src="/wp-content/uploads/2014/02/01_CreateSiteStep1-300x199.png" alt="Create Azure Web Site - Step 1" width="300" height="199" class="size-medium wp-image-2373" srcset="/wp-content/uploads/2014/02/01_CreateSiteStep1-300x199.png 300w, /wp-content/uploads/2014/02/01_CreateSiteStep1.png 700w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Create Azure Web Site &#8211; Step 1
  </p>
</div>

The wizard has a github option, but due to the way github permissions work, it ends up needing far more permissions than I want to provide. Instead I have chosen to use the generic &#8220;External Repository&#8221; option. 

<div id="attachment_2374" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/01_CreateSiteStep2.png"><img src="/wp-content/uploads/2014/02/01_CreateSiteStep2-300x198.png" alt="Create Azure Web Site - Step 2" width="300" height="198" class="size-medium wp-image-2374" srcset="/wp-content/uploads/2014/02/01_CreateSiteStep2-300x198.png 300w, /wp-content/uploads/2014/02/01_CreateSiteStep2.png 700w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Create Azure Web Site &#8211; Step 2
  </p>
</div>

The last step is to provide those repository details

<div id="attachment_2375" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/01_CreateSiteStep3.png"><img src="/wp-content/uploads/2014/02/01_CreateSiteStep3-300x191.png" alt="Create Azure Web Site - Step 3" width="300" height="191" class="size-medium wp-image-2375" srcset="/wp-content/uploads/2014/02/01_CreateSiteStep3-300x191.png 300w, /wp-content/uploads/2014/02/01_CreateSiteStep3.png 700w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Create Azure Web Site &#8211; Step 3
  </p>
</div>

And the Azure Website is running:

<div id="attachment_2376" style="width: 710px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/01_CreateSiteStep4.png"><img src="/wp-content/uploads/2014/02/01_CreateSiteStep4.png" alt="Azure Web Site - Running" width="700" height="27" class="size-full wp-image-2376" srcset="/wp-content/uploads/2014/02/01_CreateSiteStep4.png 700w, /wp-content/uploads/2014/02/01_CreateSiteStep4-300x11.png 300w" sizes="(max-width: 700px) 100vw, 700px" /></a>
  
  <p class="wp-caption-text">
    Azure Web Site &#8211; Running
  </p>
</div>

And my basic little web page is picked up by Azure Websites and deployed to the site:
  


<div id="attachment_2377" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/01_CreateSiteStep5.png"><img src="/wp-content/uploads/2014/02/01_CreateSiteStep5-300x144.png" alt="Initial Deployments" width="300" height="144" class="size-medium wp-image-2377" srcset="/wp-content/uploads/2014/02/01_CreateSiteStep5-300x144.png 300w, /wp-content/uploads/2014/02/01_CreateSiteStep5.png 700w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Initial Deployments
  </p>
</div>

Giving us the &#8220;cloud&#8221; portion of the project name:

<div id="attachment_2378" style="width: 398px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/01_CreateSiteStep6.png"><img src="/wp-content/uploads/2014/02/01_CreateSiteStep6.png" alt="It&#039;s in the cloud!" width="388" height="165" class="size-full wp-image-2378" srcset="/wp-content/uploads/2014/02/01_CreateSiteStep6.png 388w, /wp-content/uploads/2014/02/01_CreateSiteStep6-300x127.png 300w" sizes="(max-width: 388px) 100vw, 388px" /></a>
  
  <p class="wp-caption-text">
    It&#8217;s in the cloud!
  </p>
</div>

There is one last step, though. Because I used the generic &#8220;External Repository&#8221; option, my code is not being deployed immediately when I commit it.

Luckily Azure Websites exposes a deployment trigger URL that we can plug into github to notify when a new commit is received.

In the Azure Website settings, we copy that &#8220;Deployment Trigger URL&#8221;:

<div id="attachment_2371" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/01_AutoDeploymentStep1.png"><img src="/wp-content/uploads/2014/02/01_AutoDeploymentStep1-300x72.png" alt="Configuring the Build triggering - Step 1" width="300" height="72" class="size-medium wp-image-2371" srcset="/wp-content/uploads/2014/02/01_AutoDeploymentStep1-300x72.png 300w, /wp-content/uploads/2014/02/01_AutoDeploymentStep1.png 700w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Configuring the Build triggering &#8211; Step 1
  </p>
</div>

And then in the github settings for our project, we paste it in as a WebHoook URL:
  


<div id="attachment_2372" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/01_AutoDeploymentStep2.png"><img src="/wp-content/uploads/2014/02/01_AutoDeploymentStep2-300x91.png" alt="Configuring the Build triggering - Step 2" width="300" height="91" class="size-medium wp-image-2372" srcset="/wp-content/uploads/2014/02/01_AutoDeploymentStep2-300x91.png 300w, /wp-content/uploads/2014/02/01_AutoDeploymentStep2.png 700w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Configuring the Build triggering &#8211; Step 2
  </p>
</div>

And now each time I commit new code to master, it runs through the automated deployment process.

## Add Unit Tests

So far, the example code just has a single page that doesn&#8217;t really do anything worth unit testing. This isn&#8217;t intended to be a real-world sample, but it does need some simple logic to test.

<pre>public ActionResult Index()
{
    return View();
}

public ActionResult Text(string text)
{
    var model = new TextModel(text);
    return View(model);
}</pre>

Now I&#8217;ve added actions and views for a simple form that asks for a piece of text, submits it, and then shows a response based on whetehr the txt is populated or not.

So we have a form:
  


<div id="attachment_2379" style="width: 310px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/02_AddAForm.png"><img src="/wp-content/uploads/2014/02/02_AddAForm-300x197.png" alt="Sample Form Web Page" width="300" height="197" class="size-medium wp-image-2379" srcset="/wp-content/uploads/2014/02/02_AddAForm-300x197.png 300w, /wp-content/uploads/2014/02/02_AddAForm.png 365w" sizes="(max-width: 300px) 100vw, 300px" /></a>
  
  <p class="wp-caption-text">
    Sample Form Web Page
  </p>
</div>

And the page it submits to:
  


<div id="attachment_2380" style="width: 185px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/02_AddAFormSubmitted.png"><img src="/wp-content/uploads/2014/02/02_AddAFormSubmitted.png" alt="Submission Page" width="175" height="162" class="size-full wp-image-2380" /></a>
  
  <p class="wp-caption-text">
    Submission Page
  </p>
</div>

This will be easy to unit test but give us the tooling we would need to do anything more complex.

The unit test project is [/CloudSiteTests][2]. My first steps are to add a reference to the web project, add nunit via Nuget, and create some tests.

The 3 initials tests I create pass locally:
  


<div id="attachment_2382" style="width: 623px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/02_MoreUnitTests.png"><img src="/wp-content/uploads/2014/02/02_MoreUnitTests.png" alt="Passing Unit Tests (NCrunch console)" width="613" height="99" class="size-full wp-image-2382" srcset="/wp-content/uploads/2014/02/02_MoreUnitTests.png 613w, /wp-content/uploads/2014/02/02_MoreUnitTests-300x48.png 300w" sizes="(max-width: 613px) 100vw, 613px" /></a>
  
  <p class="wp-caption-text">
    Passing Unit Tests (NCrunch console)
  </p>
</div>

Now I add a fourth test to handle the case where I submit the form with an empty input.

<div id="attachment_2381" style="width: 676px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/02_FailingUnitTest.png"><img src="/wp-content/uploads/2014/02/02_FailingUnitTest.png" alt="And now a failing one (NCrunch Console)" width="666" height="130" class="size-full wp-image-2381" srcset="/wp-content/uploads/2014/02/02_FailingUnitTest.png 666w, /wp-content/uploads/2014/02/02_FailingUnitTest-300x58.png 300w" sizes="(max-width: 666px) 100vw, 666px" /></a>
  
  <p class="wp-caption-text">
    And now a failing one (NCrunch Console)
  </p>
</div>

With a failing test in the mix, now we can customize our deployment to run the unit tests and we will know for sure when they are being run correctly.

## Customize the Deployment

The next step is to create a custom deployment so we can modify it to also run the unit tests. The [Azure Command Line Tools][3] includes a set of commands to generate a basic deployment. We can then take this basic process and tune it to our needs.

**1.** Install node.js, as azure cli runs on node: http://nodejs.org/

**2.** Next we need to install the azure cli package. In a command line, run the following:

`npm install azure-cli -g`

This uses the node.js package manager to install the azure-cli package and installs it for global (-g) use, rather than for an individual project.

**3.** Open up the root solution folder and check out the options we can use with cli command:

`run: azure site deploymentscript -h`

**4.** To generate a deployment script, i&#8217;ll specify the aspWAP option and point to the project:

`azure site deploymentscript --aspWAP CloudSite/CloudSite.csproj -s CloudPixiesAndGhosts.sln`

This generates a .deployment and a deploy.cmd file.

**5.** Run the deploy.cmd file to test it out

I received an error on my first run because nuget wasn&#8217;t available. I enabled package restore on the solution and tried again and it worked. Later I found the same section (Deployment section, subsection 1) of this script was failing on Azure, so I ended up commenting it out entirely.

This may no longer be an issue, the Azure team has been updating things frequently and newer versions may be improved since.

**6.** Edit the deploy.cmd file to run my nunit tests (after adding the nunit executable to my project):

I added the following section to the command file to run my tests:

<pre>:: 2. Tests
echo 2: Build and execute tests

echo 2a: Executing Unit Tests: CloudSiteTests
%MSBUILD_PATH% "%DEPLOYMENT_SOURCE%\CloudSiteTests\CloudSiteTests.csproj" /nologo /verbosity:m /t:Build /p:Configuration=Debug
call "tools/nunit-console.exe" "%DEPLOYMENT_SOURCE%\CloudSiteTests\bin\Debug\CloudSiteTests.dll"

IF !ERRORLEVEL! NEQ 0 goto error</pre>

Now when I commit all of these changes and the deployment runs in Azure, I get the following result:

<div id="attachment_2384" style="width: 710px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/02_UnitTestFailingDeployment.png"><img src="/wp-content/uploads/2014/02/02_UnitTestFailingDeployment.png" alt="Unit Test Failing Deployment" width="700" height="195" class="size-full wp-image-2384" srcset="/wp-content/uploads/2014/02/02_UnitTestFailingDeployment.png 700w, /wp-content/uploads/2014/02/02_UnitTestFailingDeployment-300x83.png 300w" sizes="(max-width: 700px) 100vw, 700px" /></a>
  
  <p class="wp-caption-text">
    Unit Test Failing Deployment
  </p>
</div>

The log for the deployment captures the results, so I can see exactly which test failed (and also, oddly, that they appeared to run the tests twice):

<div id="attachment_2385" style="width: 710px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/02_UnitTestResultsInLog.png"><img src="/wp-content/uploads/2014/02/02_UnitTestResultsInLog.png" alt="Detailed Log of Deployment Failure" width="700" height="489" class="size-full wp-image-2385" srcset="/wp-content/uploads/2014/02/02_UnitTestResultsInLog.png 700w, /wp-content/uploads/2014/02/02_UnitTestResultsInLog-300x209.png 300w" sizes="(max-width: 700px) 100vw, 700px" /></a>
  
  <p class="wp-caption-text">
    Detailed Log of Deployment Failure
  </p>
</div>

And then when I add some logic to make the test pass, I get this result:

<div id="attachment_2383" style="width: 710px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/02_RanTestsAndDeployed.png"><img src="/wp-content/uploads/2014/02/02_RanTestsAndDeployed.png" alt="Passing Unit tests and Successful Deployment" width="700" height="236" class="size-full wp-image-2383" srcset="/wp-content/uploads/2014/02/02_RanTestsAndDeployed.png 700w, /wp-content/uploads/2014/02/02_RanTestsAndDeployed-300x101.png 300w" sizes="(max-width: 700px) 100vw, 700px" /></a>
  
  <p class="wp-caption-text">
    Passing Unit tests and Successful Deployment
  </p>
</div>

And there we have it, Nunit unit tests running automatically during the deployment every time I commit to master.

## Sidenote: CassiniDev

As I mentioned before, I originally had set out to get CassiniDev, Selenium, and Phantom all running in harmony as part of the build. I got CassiniDev and Selenium running locally, but couldn&#8217;t get CassiniDev to host the site during the deployment:

<div id="attachment_2370" style="width: 710px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/03_CassiniFailure.png"><img src="/wp-content/uploads/2014/02/03_CassiniFailure.png" alt="Network Permissions Denied for CassiniDev" width="700" height="306" class="size-full wp-image-2370" srcset="/wp-content/uploads/2014/02/03_CassiniFailure.png 700w, /wp-content/uploads/2014/02/03_CassiniFailure-300x131.png 300w" sizes="(max-width: 700px) 100vw, 700px" /></a>
  
  <p class="wp-caption-text">
    Network Permissions Denied for CassiniDev
  </p>
</div>

During the setup of the test, CassiniDev&#8217;s StartServer command receives an &#8220;Access Denied&#8221; exception.

This is the [/CloudPixiesTests][4] project in the solution, if you&#8217;re curious. The Selenium + CassiniDev (ie, the magix pixies part of the repository name) runs fine locally. 

## Would I Use This For Production?

Personally, I wouldn&#8217;t use this as my test and deployment process. I think it&#8217;s a great system, but I miss too much from having a build server and have a high enough comfort level with build servers that this simplified setup doesn&#8217;t win me much (especially once you add in the limits on what you can do with the built-in staging platform).

I really like the deployment side of this and could see using it as the last mile, with a build server that automatically managed the build process and then either pushes to a repository or merges from the branch it&#8217;s building to the one that gets deployed. The only downside to this method would be applying schema updates to storage resources (SQL or serialized documents), as the actual deployment would then be operating asynchronously from your build process, but even that is solvable.

 [1]: https://github.com/tarwn/CloudPixiesAndGhosts "tarwn/CloudPixiesAndGhosts on github"
 [2]: https://github.com/tarwn/CloudPixiesAndGhosts/tree/master/CloudSiteTests
 [3]: http://www.windowsazure.com/en-us/documentation/articles/command-line-tools/
 [4]: https://github.com/tarwn/CloudPixiesAndGhosts/tree/master/CloudPixiesTests