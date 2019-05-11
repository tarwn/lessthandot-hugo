---
title: Automated Deployment to Azure Hosted Services
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-05-27T18:50:21+00:00
ID: 2644
url: /index.php/enterprisedev/cloud/azure/automated-deployment-to-azure-hosted-services/
views:
  - 11791
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - automation
  - azure
  - deployment
  - powershell

---
Azure Hosted Services offers several really awesome features over using physical servers or standard VM infrastructure. Two of these are the staged deployment model and management SDK, which includes a powershell module. Using these two features, we are going to build a deployment script that deploys a new set of services (servers) in Azure, using a Virtual IP swap to replace the existing production instances only after the new deployment is fully running. 

The goal of this post is to build a powershell script that will:

  * Upload a compiled Package to Azure Storage
  * Create a new Staging deployment
  * Wait for all of the instances of the new deployment to be running
  * Promote the new deployment to Production
  * Stop the instances of the old production deployment and keep them handy in the Staging slot

The sample project and script are available on github: [tarwn/AzureHostedServiceDeploymentSample on github][1]

This script is not intended to be production ready. I have spent no time at all refactoring into readily re-usable methods and do not use it in a production environment myself. It will show you how to use the individual methods and give you the pieces you need to build one that fits your processes.

## Initial Steps

If you would like to build a sample project of your own and follow along, here's the steps you will need to perform first:

<ol style="margin-left:3em; line-height: 1.4em">
  <li>
    Create an Azure project in Visual Studio – Create/attach one or more web or worker roles
  </li>
  <li>
    Remove the Diagnostics entry in the web.config or add storage settings
  </li>
  <li>
    In the Project References, select "Microsoft.Web.Infrastructure" and set "Copy Local" to "True"
  </li>
  <li>
    Create a Hosted Service in the Azure Dashboard
  </li>
  <li>
    Create a Storage Account in the Azure Dashboard (pick the same region as prior step)
  </li>
  <li>
    Install the latest Azure SDK + Azure Powershell Module (available in Web Platform Installer)
  </li>
  <li>
    Download your publish settings from https://windows.azure.com/download/publishprofile.aspx
  </li>
</ol>

If you know your way around Azure, steps 4-7 are mostly reading [xkcd][2] while the installers run.

## Create the Deployment Script

Now that we have a project and all the prerequisites out of the way, let's start building the script. As a reminder, these are the steps we intend to follow:

  * Upload a compiled Package to Azure Storage
  * Create a new Staging deployment
  * Wait for all of the instances of the new deployment to be running
  * Promote the new deployment to Production
  * Suspend the instances of the old production deployment and keep them handy in the Staging slot

Let's go!

### Connect to Azure

The first thing we need to do is import the Powershell module and use the publish settings to set our subscription.

```powershell
Import-Module "C:\Program Files (x86)\Microsoft SDKs\Windows Azure\PowerShell\ServiceManagement\Azure\Azure.psd1"

Import-AzurePublishSettingsFile $publishSettingsPath
 
Set-AzureSubscription $subscriptionName -CurrentStorageAccount $storageAccountName

Select-AzureSubscription $subscriptionName -Current
```
_$publishSettingsPath, $subscriptionName, and $storageAccountName are parameters I have passed into my script_

We load the Azure module from the Microsoft SDKs folder (this is where it installs from Web PI). We then use the *.publishsettings file to "log in" to the Azure subscription, set the storage account we will be using by default, and set this subscription as the default one for our current powershell session.

<div style="background-color: #eeeeee; padding: .5em 1em; margin: .5em .5em 1.5em .5em;">
  <a href="http://msdn.microsoft.com/en-us/library/dn722512.aspx" title="Import-AzurePublishSettingsFile on MSDN">Import-AzurePublishSettingsFile</a> basically logs into your Azure account using the supplied publishsettings file, storing a management certificate and a subscription data file. Once we're "logged in", we can use the rest of the Azure cmdlets to interact with our Azure resources.</p> 

<p>
  <a href="http://msdn.microsoft.com/en-us/library/dn722501.aspx" title="Set-AzureSubscription on MSDN">Set-AzureSubscription</a> sets the "current" storage account for the subscription, basically defining a default so we don't have to specify it throughout the script. Another option would be to use <a href="http://msdn.microsoft.com/en-us/library/dn495246.aspx" title="New-AzureStorageContext on MSDN">New-AzureStorageContext</a> to create context for the Storage Account and pass this to the calls that interact with Storage.
</p>

<p>
<a href="http://msdn.microsoft.com/en-us/library/dn722499.aspx" title="Select-AzureSubscription">Select-AzureSubscription</a> does exactly what you would expect, it updates the subscription data in our Powershell context. By specifying -Current, we only update the subscription for our current session. </div> 

<h3>
  Upload a compiled Package to Azure Storage
</h3>

<p>
  Now that we have access to Azure, we can move on to upload the package. This package can be generated from Visual Studio by right clicking on the Cloud Project and choosing "Package". In an automated process, we can use MSBuild to create this package before calling this script to upload and deploy it.
</p>
    
```powershell
$container = Get-AzureStorageContainer -Name $containerName -ErrorAction SilentlyContinue

if(!$container){
    New-AzureStorageContainer -Name $containerName
}

Set-AzureStorageBlobContent -File $packagePath -Container $containerName `
                            -Blob $fullTargetPackageName -Force

$blobInfo = Get-AzureStorageBlob  -Container $containerName -blob $fullTargetPackageName

$packageUri = $blobInfo.ICloudBlob.Uri
```

<p>
<i>$packagePath and $containerName are parameters passed to the script, $fullTargetPackageName is generated with a timestamp.</i>
</p>

<p>
First we create the container if it doesn't already exist, then we upload the package (without prompting), and once that is complete we capture the blob information and extract the URL for later use in the deployment.
</p>

<div style="background-color: #eeeeee; padding: .5em 1em; margin: .5em .5em 1.5em .5em;">
<a href="http://msdn.microsoft.com/en-us/library/dn495272.aspx" title="Get-AzureStorageContainer on MSDN">Get-AzureStorageContainer</a> attempts to retrieve a container with the given name. In this case I've used the ErrorAction of SilentlyContinue so that if it doesn't exist I can create it.</p> 

<p>
<a href="http://msdn.microsoft.com/en-us/library/dn495291.aspx" title="New-AzureStorageContainer on MSDN">New-AzureStorageContainer</a> creates a container with the given name. Since I haven't specified permissions, the container will be created with the most restrictive rights.
</p>

<p>
<a href="http://msdn.microsoft.com/en-us/library/dn495279.aspx" title="Set-AzureStorageBlobContent on MSDN">Set-AzureStorageBlobContent</a> uploads the contents of a file specified by -File to the given -Container value with a final name specified by the -Blob property. The -Force overrides any questions the command might have, like "are you sure you want to do that".
</p>

<p>
<a href="http://msdn.microsoft.com/en-us/library/dn495260.aspx" title="Get-AzureStorageBlob on MSDN">Get-AzureStorageBlob</a> retrieves the information about a given Blob, allowing us to extract the Uri property for later use. </div> 

<h3>
  Create a new Staging Deployment
</h3>

<p>
  Once we have the package uploaded to blob storage, we are ready to create the new Staging deployment.
</p>

```powershell
$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot Staging `
                                  -ErrorAction SilentlyContinue 

if($deployment.name -ne $null){
    Remove-AzureDeployment -ServiceName $serviceName -Slot Staging -Force
}

New-AzureDeployment -ServiceName $serviceName -Slot Staging -Package $packageUri `
                    -Configuration $configPath -Name $fullTargetDeploymentName `
                    -TreatWarningsAsError
```

<p>
<i>The $servicename, $fulltargetDeploymentName, and $configPath are assumed to have been provided, while the $packageUri was defined in the previous step</i>
</p>

<p>
Before we can create the new deployment, we check to see if there is already a deployment present in the Staging slot and delete it. We then create the new deployment, using the package we just uploaded and a local configuration (*.cscfg) file.
</p>

<div style="background-color: #eeeeee; padding: .5em 1em; margin: .5em .5em 1.5em .5em;">
<a href="http://msdn.microsoft.com/en-us/library/dn495146.aspx" title="Get-AzureDeployment on MSDN">Get-AzureDeployment</a> retrieves details on the current deployment in the specified slot. I've used ErrorAction SilentlyContinue here because I am only making this call to determine if something is already there and don't want to exit out if the slot turns out to be empty.</p> 

<p>
<a href="http://msdn.microsoft.com/en-us/library/dn495296.aspx" title="Remove-AzureDeployment on MSDN">Remove-AzureDeployment</a> removes the deployment we have detected in the Staging slot, using -Force to again suppress any interactive questions the command might have.
</p>

<p>
<a href="http://msdn.microsoft.com/en-us/library/dn495143.aspx" title="New-AzureDeployment on MSDN">New-AzureDeployment</a> creates a new deployment in the specified slot, using the supplied package URI and the configuration file path. I opted to treat warnings as errors because I'd rather clean up warnings immediately. Unfortunately this parameter does not support URLs. By default the deployment will be started, though there is a -DoNotStart parameter if you do not want this behavior. </div> 

<h3>
  Wait for all of the instances...
</h3>

<p>
  The new deployment has been created and told to start, but it takes time for the individual instances to be provisioned and to go through their start-up sequence.
</p>

```powershell
$statusReady = "ReadyRole"
$statusStopped = "StoppedVM"

function Get-AllInstancesAreStatus($instances, $targetStatus){
    foreach ($instance in $instances)
    {
        if ($instance.InstanceStatus -ne $targetStatus)
        {
            return $false
        }
    }
    return $true
}

# ... ... ...

$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot Staging

$waitTime = [System.Diagnostics.Stopwatch]::StartNew()
while ((Get-AllInstancesAreStatus $deployment.RoleInstanceList $statusReady) -eq $false)
{
    if($waitTime.Elapsed.TotalSeconds -gt $instancePollLimit){
        Throw "$instancePollLimit seconds elapsed without all the instances reaching 'ReadyRun'"
    }

    Start-Sleep -Seconds $instancePollRate

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot Staging
}
```

<p>
  <i>$serviceName is supplied as a script parameter.</i>
</p>

<p>
  While there are any instances that are not in 'ReadyRun' status, we sleep for $instancepollRate seconds and continue to check again. If more than $instancePollLimit seconds go by while waiting, we'll throw an error that will cause our script to exit.
</p>

<p>
  <b>This poll limit is necessary.</b> In the real world of Azure, you can have azure instances that do not boot for long periods of time. Additional logic has been added in Azure that is supposed to detect VMs not booting and replace them, but no one writes perfect code and I have experienced deployments hung for hours or more due to non-booting instances. We also can break our own code, resulting in rapidly re-booting instances that we would not want to deploy to production.
</p>

<div style="background-color: #eeeeee; padding: .5em 1em; margin: .5em .5em 1.5em .5em;">
  <a href="http://msdn.microsoft.com/en-us/library/dn495146.aspx" title="Get-AzureDeployment on MSDN">Get-AzureDeployment</a> gets the azure deployment details, including the list of instances with their names, current statuses, size, etc.
</div>

<h3>
  Promote the new deployment to Production, Suspend the old one
</h3>

<p>
  Once the staging deployment is up and running, we can promote it to the Production slot.
</p>

```powershell
Move-AzureDeployment -ServiceName $serviceName

$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot Staging `
                                  -ErrorAction SilentlyContinue

if($deployment.DeploymentName -ne $null){
    Set-AzureDeployment -Status -ServiceName $serviceName -Slot Staging `
                        -NewStatus Suspended
}

Remove-AzureAccount -Name $subscriptionName -Force
```

<p>
<i>$serviceName is a parameter passed to the script</i>
</p>

<p>
Performing the VIP swap is a simple command and the Powershell cmdlet turns that asynchronous method into a synchronous call for us, like so many of the others. Once the swap is complete, if we have a deployment in the Staging slot (the old Production one), we go ahead and tell it to suspend, but don't wait for the individual instances to stop before exiting.
</p>

<div style="background-color: #eeeeee; padding: .5em 1em; margin: .5em .5em 1.5em .5em;">
<a href="http://msdn.microsoft.com/en-us/library/dn495282.aspx" title="Move-AzureDeployment">Move-AzureDeployment</a> performs a VIP swap to swap the Staging and Production deployments.</p> 

<p>
<a href="http://msdn.microsoft.com/en-us/library/dn495146.aspx" title="Get-AzureDeployment on MSDN">Get-AzureDeployment</a> gets the azure deployment details, including the list of instances with their names, current statuses, size, etc.
</p>

<p>
<a href="http://msdn.microsoft.com/en-us/library/dn495140.aspx" title="Set-AzureDeployment">Set-AzureDeployment</a> with the -Status parameter is used to change the status of a given deployment, in this case Suspending the deployment in the Staging slot.
</p>

<p>
<a href="http://msdn.microsoft.com/en-us/library/dn722529.aspx" title="Remove-AzureAccount on MSDN">Remove-AzureAccount</a> is used to remove the Azure subscription data from the Powershell session, basically the "logout" equivalent to Import-AzurePublishSettingsFile's "login" </div> 

<h2>
  And we're deployed...
</h2>

<p>
  There is a full script available in the github repository here: <a href="https://github.com/tarwn/AzureHostedServiceDeploymentSample/blob/master/scripts/deployHostedService.ps1" title="/scripts/deployHostedService.ps1">/scripts/deployHostedService.ps1</a>. It is not clean and pretty, but it does have more output and error handling than the snippets above. Among other things, it does not clean out all those packages it uploads to blob storage and it most definitely should not be blindly pasted and used for your production environment.
</p>

<p>
  While this may not be a production-ready script, it's not far off (and I've used worse). The few cmdlets above should start to show the pattern that Microsoft used with this Powershell library. There are plenty of additional cmdlets to interact with storage services, VMs, affinity groups, HDInsight, Media Services...you name it, it's probably in there.
</p>

<p>
  Writing this post, I am reminded how magical this all is. That sample project was only configured to ask for a single server, but I could just as easily have asked for 4 16-core servers and then added in additional web or worker roles, each with their own servers. And I could have done all of that without changing anything at all about this script and I would have had tons of servers deployed, load balanced, and ready to go with just a minor blip as I swapped them into production. I can remember projects with multi-hour manual deployment processes (and month or more system provisioning times), and we just replaced them with a one page script.
</p>

<p>
  The best part is that, unlike some Microsoft frameworks/packages, this magic doesn't just make a great demo, it also works in real production environments.
</p>

 [1]: https://github.com/tarwn/AzureHostedServiceDeploymentSample "tarwn/AzureHostedServiceDeploymentSample on github"
 [2]: http://xkcd.com/ "If we are what we eat, what could be better for our brains than a steady diet of intelligent humor? And title tags, you have to love title tags."