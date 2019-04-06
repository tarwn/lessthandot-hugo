---
title: Deploying to ServiceFabric from TeamCity
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-08-07T12:30:56+00:00
url: /index.php/enterprisedev/cloud/azure/deploying-to-servicefabric-from-teamcity/
views:
  - 3252
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - azure
  - powershell
  - Service Fabric
  - teamcity

---
Recently I&#8217;ve been working on an application that runs partially in Azure ServiceFabric. I&#8217;ve [created a local cluster]() to work against and now it&#8217;s time to configure my TeamCity deployment to deploy upgrades to my application automatically.

Initial details:

  * Deploying 2 projects: 
      * a .Net 4.6.2 ASP.Net Core app to web app
      * .Net 4.6.2 ServiceFabric project to ServiceFabric cluster
  * Server: 
      * VM: 2-core, 2048 GB of RAM
      * Windows Server 2016 x64
      * TeamCity 2017.1
      * SQL Server 2016

In the previous post I walked through the setup of a Service Fabric cluster on a local Hyper-V server and 3 VMs, followed by publishing a Service Fabric service manually to the cluster. In this post, I&#8217;m evolving from the manual publish step to a TeamCity automated deployment.

Here are the system details:

  * **TeamCity**: VM w/ 2 cores assigned and 2048 MB of memory running Windows 2016 x64 with TeamCity 2017.1 and SQL Server 2016
  * **ServiceFabricNodes:** 3 single core VMs w/ 2048 MB of memory running Windows 2016 x64

Let&#8217;s go!

## Installing Dependencies

This solution has two deliverables: a front-end API that is deployed to Azure Web Site (now App Service) and a back-end agent intended to run in Service Fabric. I&#8217;ve already setup a Continuous Integration step to build the projects, run the database migration, perform front-end gulp tasks, and verify a set of unit and integration tests, so I should have most of the dependencies I need.

I&#8217;ve installed VS 2017 Community, VS 2017 Build tools, [Service Fabric SDK 2.6.220][1], Node.js 6.11.1 (LTS), NuGet 4.1, and [jonnyzzz&#8217;s Node plugin][2]. 

<div style="background-color: #eeeeee; margin: 1em; padding: 1em">
  I installed VS 2017 because historically I&#8217;ve run into issues with Azure projects (and MVC before that, and parts of WebForms before that). I originally was opposed to have the IDE installed on the build server, but have since decided I don&#8217;t mind and look at it as building with the same toolset on the build server that I built and tested with locally.
</div>

This is also the place I ran into the madness that is the current [mess of NuGet and C# Projects][3].

## Deploying the Service Fabric Project

My build pipeline for this project will be a single CI stage that runs the tests for both projects, and package stage that packages releasable versions for both projects, then two independent deploy stages to deploy the website and service fabric packages to their appropriate places. 

<div id="attachment_8781" style="width: 557px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/08/DeployserviceFabric.png" alt="Deployment Pipeline - API updates before ServiceFabric" width="547" height="129" class="size-full wp-image-8781" srcset="/wp-content/uploads/2017/08/DeployserviceFabric.png 547w, /wp-content/uploads/2017/08/DeployserviceFabric-300x71.png 300w" sizes="(max-width: 547px) 100vw, 547px" />
  
  <p class="wp-caption-text">
    Deployment Pipeline &#8211; API updates before ServiceFabric
  </p>
</div>

This is my &#8220;good enough for now&#8221; setup. If I run into versioning issues, I can come back and add some backwards compatibility tests between the packages after CI and run the deployments serially instead of in parallel.

<div id="attachment_8756" style="width: 310px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/DeployScript-300x172.png" alt="Service Fabric Deploy Script" width="300" height="172" class="size-medium wp-image-8756" srcset="/wp-content/uploads/2017/07/DeployScript-300x172.png 300w, /wp-content/uploads/2017/07/DeployScript.png 520w" sizes="(max-width: 300px) 100vw, 300px" />
  
  <p class="wp-caption-text">
    Service Fabric Deploy Script
  </p>
</div>

The Service Fabric package is created with a `Deploy-FabricApplication.ps1` script that we&#8217;ll use to deploy the package once it&#8217;s built.

**Local Script Deploy to ServiceFabric**

The first step is testing the script locally to make sure I know how to use it. 

<ol style="padding-left: 40px">
  <li>
    Right click the Service Fabric project and select &#8220;Package&#8221;
  </li>
  <li>
    Open a powershell console and direct it to the Scripts folder in the Service Fabric project
  </li>
  <li>
    Run a sample deployment with any parameters you have in the file: <code>.\Deploy-FabricApplication.ps1 -ApplicationPackagePath '..\pkg\Debug\' -PublishProfileFile '..\PublishProfiles\Local.1Node.xml'  -ApplicationParameter @{PhantomAgent_InstanceCount='1'; CoordinatorURL='http://localhost:52860' }</code>
  </li>
</ol>

I had to do this several times, so I also got to learn how to update versions:

<ol style="padding-left: 40px">
  <li>
    Add UpgradeDeployment to your PublishProfile: <ul style="padding-left: 40px">
      <li>
        Option 1: Right click in Visual Studio, select Publish, use the link near the bottom to edit your deployment options and then close the dialog, choose &#8220;yes&#8221; when it asks if you want to save the profile&#8221;
      </li>
      <li>
        Option 2: Open the relevant PublishProfile XML file and add this to the bottom for the default Unmonitored Upgrade settings <pre>&lt;UpgradeDeployment Mode="UnmonitoredAuto" Enabled="true"&gt;
    &lt;Parameters UpgradeReplicaSetCheckTimeoutSec="1" Force="True" /&gt;
&lt;/UpgradeDeployment&gt;</pre>
      </li>
    </ul>
  </li>
  
  <li>
    Go to the pkg\Debug
  </li>
  <li>
    Open [YourProject]Pkg\ServiceManifest.xml <ol style="padding-left: 40px">
      <li>
        Update the version in either <code>&lt;CodePackage Name="Code" Version="1.0.3"&gt;</code> or <code>&lt;ConfigPackage Name="Config" Version="1.0.4" /&gt;</code>
      </li>
      <li>
        Update the Package version in the <code>&lt;ServiceManifest … Version="1.0.2" …&gt;</code> root element
      </li>
      <li>
        Save
      </li>
    </ol>
  </li>
  
  <li>
    Open ApplicationManifest.xml <ol style="padding-left: 40px">
      <li>
        Find <code>&lt;ServiceManifestRef ..&gt;</code> and update ServiceManifestVersion to match the ServiceManifest version above
      </li>
      <li>
        Update the ApplicationTypeVersion property in the <code>&lt;ApplicationManifest … &gt;</code> root element
      </li>
      <li>
        Save
      </li>
    </ol>
  </li>
  
  <li>
    Now try your deployment again!
  </li>
</ol>

So now we have a working command locally, now we just have to get TeamCity to update versions appropriately and run this. 

### Build the package

Building the package is straightforward. We just need to build the package in release mode, then configure Archiving to capture the bin/Release folder and PublishProfiles folder.

The Build Step in my &#8220;Package Stage&#8221; configuration looks like this:

<div id="attachment_8757" style="width: 1034px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/BuildPackageStage-1024x730.png" alt="TeamCity Package Stage" width="1024" height="730" class="size-large wp-image-8757" srcset="/wp-content/uploads/2017/07/BuildPackageStage-1024x730.png 1024w, /wp-content/uploads/2017/07/BuildPackageStage-300x214.png 300w, /wp-content/uploads/2017/07/BuildPackageStage-768x548.png 768w, /wp-content/uploads/2017/07/BuildPackageStage.png 1491w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    TeamCity Package Stage
  </p>
</div>

Once I have the package built, I am going to replace the versions with a value that ties to the TeamCity version number.

In the &#8220;General&#8221; tab in TeamCity, I use the build number token from my CI step as the version for this step:

<div id="attachment_8758" style="width: 1034px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/TeamCity_Package_BuildNumber-1024x51.png" alt="TeamCity Package Build Number" width="1024" height="51" class="size-large wp-image-8758" srcset="/wp-content/uploads/2017/07/TeamCity_Package_BuildNumber-1024x51.png 1024w, /wp-content/uploads/2017/07/TeamCity_Package_BuildNumber-300x15.png 300w, /wp-content/uploads/2017/07/TeamCity_Package_BuildNumber-768x38.png 768w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    TeamCity Package Build Number
  </p>
</div>

My CI step has this for it&#8217;s build number:

<div id="attachment_8759" style="width: 1034px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/TeamCity_CI_BuildNumber-1024x73.png" alt="TeamCity CI Build Number" width="1024" height="73" class="size-large wp-image-8759" srcset="/wp-content/uploads/2017/07/TeamCity_CI_BuildNumber-1024x73.png 1024w, /wp-content/uploads/2017/07/TeamCity_CI_BuildNumber-300x21.png 300w, /wp-content/uploads/2017/07/TeamCity_CI_BuildNumber-768x54.png 768w, /wp-content/uploads/2017/07/TeamCity_CI_BuildNumber.png 1510w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    TeamCity CI Build Number
  </p>
</div>

So now I will have a matching 1.X.0 value all the way from CI through to the ServiceFabric manager.

Then in a new build step, I replace the versions in my two manifest files with the build version token from TeamCity:

<div id="attachment_8760" style="width: 1034px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/TeamCity_Package_Versions-1024x201.png" alt="TeamCity Package Versions" width="1024" height="201" class="size-large wp-image-8760" srcset="/wp-content/uploads/2017/07/TeamCity_Package_Versions-1024x201.png 1024w, /wp-content/uploads/2017/07/TeamCity_Package_Versions-300x59.png 300w, /wp-content/uploads/2017/07/TeamCity_Package_Versions-768x151.png 768w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    TeamCity Package Versions
  </p>
</div>

Finally, I add entries to the &#8220;Artifact Paths&#8221; back in the &#8220;General&#8221; tab to zip up the package for use by the next step:

<div id="attachment_8761" style="width: 1034px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/TeamCity_Package_Artifacts-1024x104.png" alt="TeamCity Package Artifacts" width="1024" height="104" class="size-large wp-image-8761" srcset="/wp-content/uploads/2017/07/TeamCity_Package_Artifacts-1024x104.png 1024w, /wp-content/uploads/2017/07/TeamCity_Package_Artifacts-300x30.png 300w, /wp-content/uploads/2017/07/TeamCity_Package_Artifacts-768x78.png 768w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    TeamCity Package Artifacts
  </p>
</div>

The final steps look like this:

<div id="attachment_8762" style="width: 1034px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/TeamCity_Package_Steps-1024x234.png" alt="TeamCity Package Steps" width="1024" height="234" class="size-large wp-image-8762" srcset="/wp-content/uploads/2017/07/TeamCity_Package_Steps-1024x234.png 1024w, /wp-content/uploads/2017/07/TeamCity_Package_Steps-300x68.png 300w, /wp-content/uploads/2017/07/TeamCity_Package_Steps-768x175.png 768w, /wp-content/uploads/2017/07/TeamCity_Package_Steps.png 1517w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    TeamCity Package Steps
  </p>
</div>

Running the build, I can verify everything is successful by opening up the archived package:

<div id="attachment_8763" style="width: 1034px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/TeamCity_Package_Success-1024x321.png" alt="TeamCity Package - Verifying Versions" width="1024" height="321" class="size-large wp-image-8763" srcset="/wp-content/uploads/2017/07/TeamCity_Package_Success-1024x321.png 1024w, /wp-content/uploads/2017/07/TeamCity_Package_Success-300x94.png 300w, /wp-content/uploads/2017/07/TeamCity_Package_Success-768x241.png 768w, /wp-content/uploads/2017/07/TeamCity_Package_Success.png 1392w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    TeamCity Package &#8211; Verifying Versions
  </p>
</div>

### Deploy

Now that I have a step building the files I need, I&#8217;ll add a new Build Configuration named &#8220;Deploy to Service Fabric&#8221;. I&#8217;ll set Snapshot and Artifact Dependencies to the prior Build Configuration and update the Build Number to use the value from that config (which is in turn using the one from CI).

<div id="attachment_8764" style="width: 922px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/TeamCity_Deploy_Dependencies.png" alt="TeamCity Deploy Dependencies" width="912" height="406" class="size-full wp-image-8764" srcset="/wp-content/uploads/2017/07/TeamCity_Deploy_Dependencies.png 912w, /wp-content/uploads/2017/07/TeamCity_Deploy_Dependencies-300x134.png 300w, /wp-content/uploads/2017/07/TeamCity_Deploy_Dependencies-768x342.png 768w" sizes="(max-width: 912px) 100vw, 912px" />
  
  <p class="wp-caption-text">
    TeamCity Deploy Dependencies
  </p>
</div>

I have one build step, a powershell command that matches the manual one I was running earlier that is set to treat powershell errors as errors (instead of the default, warnings). I run this as a single PowerShell source script so I can use dot notation (ServiceFabric scripts make some assumptions about having the connection variable available):

<pre>Invoke-Expression ". .\Deploy-FabricApplication.ps1 -ApplicationPackagePath ../../../Artifacts -PublishProfileFile ../PublishProfiles/LocalCluster.xml -ApplicationParameter @{PhantomAgent_InstanceCount='1'; CoordinatorURL='http://app.launchready.co'}"</pre>

My build step then runs this command like so:

<div id="attachment_8765" style="width: 1034px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/TeamCity_Deploy_Script-1024x272.png" alt="TeamCity Deploy Script" width="1024" height="272" class="size-large wp-image-8765" srcset="/wp-content/uploads/2017/07/TeamCity_Deploy_Script-1024x272.png 1024w, /wp-content/uploads/2017/07/TeamCity_Deploy_Script-300x80.png 300w, /wp-content/uploads/2017/07/TeamCity_Deploy_Script-768x204.png 768w, /wp-content/uploads/2017/07/TeamCity_Deploy_Script.png 1226w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    TeamCity Deploy Script
  </p>
</div>

Note 1: Make sure your Server certificate is installed and permission granted to the user that TeamCity runs under.

Note 2: I also had to alter my LocalCluster.xml profile to `StoreLocation="LocalMachine"` instead of `StoreLocation="CurrentUser"`, since that I where I installed the certificate.

And there we have it:

<div id="attachment_8766" style="width: 387px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/TeamCity_Deploy_Success.png" alt="TeamCity Deploy Success for v1.43.0" width="377" height="104" class="size-full wp-image-8766" srcset="/wp-content/uploads/2017/07/TeamCity_Deploy_Success.png 377w, /wp-content/uploads/2017/07/TeamCity_Deploy_Success-300x83.png 300w" sizes="(max-width: 377px) 100vw, 377px" />
  
  <p class="wp-caption-text">
    TeamCity Deploy Success for v1.43.0
  </p>
</div>

<div id="attachment_8767" style="width: 817px" class="wp-caption aligncenter">
  <img src="/wp-content/uploads/2017/07/ServiceFabric_Deploy_Success.png" alt="ServiceFabric Success for v1.43.0" width="807" height="98" class="size-full wp-image-8767" srcset="/wp-content/uploads/2017/07/ServiceFabric_Deploy_Success.png 807w, /wp-content/uploads/2017/07/ServiceFabric_Deploy_Success-300x36.png 300w, /wp-content/uploads/2017/07/ServiceFabric_Deploy_Success-768x93.png 768w" sizes="(max-width: 807px) 100vw, 807px" />
  
  <p class="wp-caption-text">
    ServiceFabric Success for v1.43.0
  </p>
</div>

We can see matching versions in both places (1.43.0), so we know the pipeline is functioning.

 [1]: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started
 [2]: https://github.com/jonnyzzz/TeamCity.Node
 [3]: /index.php/itprofessionals/softwareandconfigmgmt/multiple-nuget-methods-for-vs2017-msbuild-15-in-teamcity/ "Multiple NuGet Methods for VS2017 + MSBuild 15 in TeamCity"