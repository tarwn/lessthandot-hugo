---
title: Creating a local Service Fabric Cluster
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-07-26T15:37:14+00:00
url: /index.php/enterprisedev/cloud/azure/creating-a-local-service-fabric-cluster/
views:
  - 4829
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - azure
  - powershell
  - Service Fabric

---
Working with Service Fabric and want a local cluster to test and develop against? Here&#8217;s the step-by-step path I took from a set of fresh Windows VMs to a running, secured Service Fabric cluster using self-signed X509 certificates. There are a number of Microsoft docs that cover this subject, this is a single beginning-to-end path that also includes fixes for gaps or errors in those docs as I went.

Here are the technical details:

  * 3 Hyper-V VMs running Windows 2016 x64, single-core, 2046MB RAM
  * ServiceFabric 5.6.220.9494

My VMs are: 

  * SFNode0 &#8211; 192.168.1.200
  * SFNode1 &#8211; 192.168.1.201
  * SFNode2 &#8211; 192.168.1.202

Here we go!

## Step 1: Download the Service Fabric Standalone Package

Starting on SFNode0, I [download the package][1]. There is a brief struggle through the overly strict IE security settings (did you know docs.microsoft.com uses google-analytics?).

Unpack the downloaded archive and make a copy of the ClusterConfig.X509.MultiMachine.json so we can modify a copy without changing the original. I&#8217;ve named this &#8220;ClusterConfig.LaunchReady.LocalCluster.json&#8221; for my cluster.

## Step 2: Cluster Configuration File

The configuration (or &#8220;manifest&#8221;) is explained in detail in [Microsoft Docs][2]. I&#8217;ll call out the specifics of what I&#8217;m changing as I go.

<div id="attachment_8718" style="width: 1034px" class="wp-caption aligncenter">
  <img src="http://blogs.ltd.local/wp-content/uploads/2017/07/SFNode0ConfigScreen.png" alt="Initial Cluster Configuration" width="1024" height="768" class="size-full wp-image-8718" srcset="http://blogs.ltd.local/wp-content/uploads/2017/07/SFNode0ConfigScreen.png 1024w, http://blogs.ltd.local/wp-content/uploads/2017/07/SFNode0ConfigScreen-300x225.png 300w, http://blogs.ltd.local/wp-content/uploads/2017/07/SFNode0ConfigScreen-768x576.png 768w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    Initial Cluster Configuration
  </p>
</div>

The first update is the name of the cluster:

<pre>"name": "LaunchReady.LocalCluster",
"clusterConfigurationVersion": "1.0.0",
"apiVersion": "04-2017",</pre>

The clusterConfigurationVersion and apiVersion can stay unchanged. Later when we make changes to the cluster, we&#8217;ll increment the clusterConfigurationVersion (and commit it to our git repository).

### Configuring Nodes

The next section is the node definitions. I&#8217;ve updated these to reflect the names of my VMs, a common fault domain to indicate the shared server they are running on, and a common update domain (it woul dbe better to make these different, I wasn&#8217;t thinking when I first created this).

<pre>{
	"nodeName": "SFNode0",
	"iPAddress": "SFNode0",
	"nodeTypeRef": "NodeType0",
	"faultDomain": "fd:/hyperv0",
	"upgradeDomain": "UD0"
},
{
	"nodeName": "SFNode1",
	"iPAddress": "SFNode1",
	"nodeTypeRef": "NodeType0",
	"faultDomain": "fd:/hyperv0",
	"upgradeDomain": "UD0"
},
{
	"nodeName": "SFNode2",
	"iPAddress": "SFNode2",
	"nodeTypeRef": "NodeType0",
	"faultDomain": "fd:/hyperv0",
	"upgradeDomain": "UD0"
}</pre>

Here&#8217;s a break down of the properties:

  * nodeName: is the name that we will see in logs and the management console.
  * iPAddress: is a discoverable name or IPAddress for the node
  * nodeTypeRef: NodeTypes are defined later in the configuration and represent port and reliability settings for the node [See MSDN][3]
  * faultDomain: An indicator of (potentially) shared physical resources that the node relies on (if this goes down, all nodes with this indicator will as well)
  * upgradeDomain: Identifier to group (or not) which nodes will be upgraded simultaneously during an upgrade

I am going to skip over the diagnosticsStore section for now, as the defaults will be good enough until I have the cluster running, which requires the configs above and the X509 configs coming up next.

### Configuring X509 certificates

More background detail: [Secure a standalone cluster on Windows using X.509 certificates][4]

I am going to secure this as if it is a production cluster, to ensure any work I do in my local lab won&#8217;t suddenly blow up when I switch to an Azure cluster, but I&#8217;ll use self-signed certificates since it is a local lab. I&#8217;ll use a single certificate for node-to-node and server-to-client (`ClusterCertificate`, `ServerCertificate`) because I don&#8217;t plan on performing certificate rollovers. I&#8217;ll have a second certificate for clients to authenticate with when connecting (`ClientCertificateThumbprints`).

<div style="background-color: #FFCCCC; padding: 1em; margin: 1em;">
  <b>Warning:</b> The <a href="https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security#optional-create-a-self-signed-certificate">Self-Signed Certificate instructions</a> are generally ok, but make some assumptions about the Service Fabric SDK, copy and paste for Certificate Thumbprints, file permissions, etc. The instructions below borrow from this document, but correct some of those deficiencies and assumptions to work in the context of following the Service Fabric setup instructions.
</div>

First, switch to a system that has the ServiceFabric SDK installed. It won&#8217;t be present on your nodes at this point. 

Next copy the CertSetup.ps1 file to your desktop or another location that will allow you to edit the file (we don&#8217;t want to replace one the SDK relies on and Windows security will prevent you from saving over it in the current location).

Next, follow the instructions to generate a cluster/server certificate and a client certificate (I named mine &#8220;LaunchReadyLocalClusterCert&#8221; and &#8220;LaunchReadyLocalClientCert&#8221;). This requires editing the names in CertSetup.ps1 on line 22 (Cleanup-Cert function), line 96, and line 163.

Launch PowerShell as an Administrator, then run the altered script `.\CertSetup.ps1 -Install`. When it completes, edit the script to enter the second certificate subject name and run it a second time.

Opening &#8220;Manage computer certificates&#8221; from the Start menu, I can see my two certificates listed in Personal/Certificates:

<div id="attachment_8719" style="width: 769px" class="wp-caption aligncenter">
  <img src="http://blogs.ltd.local/wp-content/uploads/2017/07/Certificates.png" alt="Certificates Successfully Generated" width="759" height="155" class="size-full wp-image-8719" srcset="http://blogs.ltd.local/wp-content/uploads/2017/07/Certificates.png 759w, http://blogs.ltd.local/wp-content/uploads/2017/07/Certificates-300x61.png 300w" sizes="(max-width: 759px) 100vw, 759px" />
  
  <p class="wp-caption-text">
    Certificates Successfully Generated
  </p>
</div>

To export these to pfx files, I copied the thumbprint from the details for each certificate and ran it like so:

<pre>$pswd = ConvertTo-SecureString -String "NotMyRealPassword!" -Force –AsPlainText

#Client cert
Get-ChildItem -Path "cert:\localMachine\my\ae 01 64 c8 27 56 71 59 e8 3b c9 37 c4 47 b8 75 7d 1c f3 7e" | Export-PfxCertificate -FilePath C:\LaunchReadyLocalClientCert.pfx -Password $pswd
#Server cert
Get-ChildItem -Path "cert:\localMachine\my\e7 98 12 6c 5c 04 46 55 ef ad f7 e3 99 88 0a 82 e7 87 c8 6f" | Export-PfxCertificate -FilePath C:\LaunchReadyLocalClusterCert.pfx -Password $pswd</pre>

<div style="background-color: #FFFFCC; padding: 1em; margin: 1em;">
  Potential Error: If you receive a null object error, you may have an invisible character at the beginning of the thumbprint. I put my cursor at the beginning of the thumbprint and pressed backspace once and was able to run the script no the next try.
</div>

With the PFX files produced, now we have to get them onto the nodes. 

The quickest solution, since I&#8217;m on the same network, is to open up a shared folder from my desktop temporarily and download to each of the 3 nodes. From the [Install the Certificates][5] instructions, I create a script to install the certs and copy their second script to set permissions and drop those in the fileshare also.

**Install my certs:**

<pre>$pswd = ConvertTo-SecureString -String "NotMyRealPassword!" -Force –AsPlainText

## Client Cert
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath "C:\LaunchReadyLocalClientCert.pfx" -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
## Server Cert
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath "C:\LaunchReadyLocalClusterCert.pfx" -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)</pre>

On each node, I copy the 4 files, run the Install script, then run the Permissions script once for each Thumbprint:

<div id="attachment_8720" style="width: 869px" class="wp-caption aligncenter">
  <img src="http://blogs.ltd.local/wp-content/uploads/2017/07/InstallCertificates.png" alt="Install Certificates and Grant Access" width="859" height="586" class="size-full wp-image-8720" srcset="http://blogs.ltd.local/wp-content/uploads/2017/07/InstallCertificates.png 859w, http://blogs.ltd.local/wp-content/uploads/2017/07/InstallCertificates-300x205.png 300w, http://blogs.ltd.local/wp-content/uploads/2017/07/InstallCertificates-768x524.png 768w" sizes="(max-width: 859px) 100vw, 859px" />
  
  <p class="wp-caption-text">
    Install Certificates and Grant Access
  </p>
</div>

Finally, I return to SFNode0 and enter the thumbprints in the &#8220;Security&#8221; section of my cluster configuration, removing the ThumbprintSecondary properties, the ClientCertificateCommonNames property, and the ReverseProxyCertificate property that I don&#8217;t intend to use.

## Step 3: Test the Configuration</h3> 

Note: Make sure you look at the paths in the fabricSettings section and move these to a non-OS drive if available. These are not changeable once the cluster is created. I chose to keep the defaults for this local cluster.

Before testing, there are some notable prerequisites buried in the [Environment Setup][6]:

  * #9: Add firewall entry to allow ports 135, 137, 138, 139, and 445

To test the configuration, I opened a powershell console on SFNode0 and run `.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.LaunchReady.LocalCluster.json`

<div style="background-color: #FFFFCC; padding: 1em; margin: 1em;">
  Tip: open a powershell console to the current folder in Windows Explorer by typing &#8220;powershell&#8221; in the address bar!
</div>

Here are the errors as I work through them:

### Name Resolution Failure

**Error:** &#8220;Machine &#8216;SFNode2&#8217; is not reachable on port 445. Check connectivity/open ports. Error: No such host is known&#8221;

**Fix:** Name resolution failed to find the host on my local network, so I switched my `iPAddress` properties to actual IP Addresses.

### Missing Firewall Rule

**Error:** &#8220;Machine &#8216;SFNode2&#8217; is not reachable on port 445. Check connectivity/open ports. Error: A connection attempt failed because the conncted party did not properly respond…&#8221; (classic timeout error)

**Fix:** Add the Firewall rule I mentioned above to allow traffic on 135, 137, 138, 139, and 445.

### SMB? Reboot all the things

**Error:** &#8220;Machine &#8216;SFNode2&#8217; is not reachable on port 445. Check connectivity/open ports. Error: The connection was actively refused&#8221;

**Fix:**
  
1. Open the Network Adapter properties and make sure &#8220;File and Printer Sharing for Microsoft Networks&#8221; is enabled (or netstat -ao and make sure you&#8217;re listening on 445)
  
2. Reboot <- It's like Windows NT all over again! (I don't know why this fixed it, but it did) <img src="http://blogs.ltd.local/wp-content/uploads/2017/07/SuccessfulConfigTest.png" alt="Successful Configuration Test" width="517" height="258" class="size-full wp-image-8721" srcset="http://blogs.ltd.local/wp-content/uploads/2017/07/SuccessfulConfigTest.png 517w, http://blogs.ltd.local/wp-content/uploads/2017/07/SuccessfulConfigTest-300x150.png 300w" sizes="(max-width: 517px) 100vw, 517px" />

Much Success!

## Step 4: Deploy the cluster</h3> 

Time to try deploying the cluster, using the provided `CreateServiceFabricCluster` script.

(cue ominous organ music)

`.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.LaunchReady.LocalCluster.json -AcceptEULA`

Here&#8217;s a summary of what the script is running (so you know it hasn&#8217;t gone off the rails):

  * Check and create if necessary: Runtime directory
  * Check and create if necessary: Trace folder
  * Download Runtime package (small delay for download time)
  * Creating Service fabric Cluster…
  * Details per machine: Configuring, Configured, Started FabricInstallerSvc, …(HDD clicky clicky)…, Started FabricHostSvc, (short delay)
  * Your cluster is successful created! …

This took a few minutes to run for me, but of course YMMV depending on internet speed, CPU resources, etc.

## Step 5: Connect to the cluster

Connecting via web browser is easy, but the documentation assumes you are using an insecure setup. `http://localhost:19080/Explorer/index.html` will time out.

Use https instead and use something like Chrome instead of IE. Chrome will popup an option for you to select the Client Certificate we produced earlier, and then connect successfully:

<div id="attachment_8722" style="width: 1034px" class="wp-caption aligncenter">
  <img src="http://blogs.ltd.local/wp-content/uploads/2017/07/ServiceFabricDashboard-1024x593.png" alt="Service Fabric Dashboard" width="1024" height="593" class="size-large wp-image-8722" srcset="http://blogs.ltd.local/wp-content/uploads/2017/07/ServiceFabricDashboard-1024x593.png 1024w, http://blogs.ltd.local/wp-content/uploads/2017/07/ServiceFabricDashboard-300x174.png 300w, http://blogs.ltd.local/wp-content/uploads/2017/07/ServiceFabricDashboard-768x444.png 768w, http://blogs.ltd.local/wp-content/uploads/2017/07/ServiceFabricDashboard.png 1360w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    Service Fabric Dashboard
  </p>
</div>

Because the certificate is self-generated, it will be treated as insecure by the browser and may require you to also go through a &#8220;no, really, I trust this certificate&#8221; routine.

## Step 6: Publish a ServiceFabric Project from VisualStudio

Switching to Visual Studio, your ServiceFabric project should have a folder named &#8220;PublishProfiles&#8221;. Make a copy of the default &#8220;Cloud.xml&#8221; profile and rename it to &#8220;LocalCluster.xml&#8221;. 

There is an example for connecting via X509 certificates in a comment in the xml file, so replace the current content with that example and edit appropriately. Use the Thumbprint from the Server certificate above (also can be found in the cluster manifest screen at https://(ip address):19080/Explorer/index.html#/tab/manifest).

My file now looks like this:

<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools"&gt;
       &lt;ClusterConnectionParameters ConnectionEndpoint="192.168.173.200:19000"
                                    X509Credential="true"
                                    ServerCertThumbprint="E798126C5C044655EFADF7E399880A82E787C86F"
                                    FindType="FindByThumbprint"
                                    FindValue="E798126C5C044655EFADF7E399880A82E787C86F"
                                    StoreLocation="CurrentUser"
                                    StoreName="My" /&gt;

  &lt;ApplicationParameterFile Path="..\ApplicationParameters\LocalCluster.xml" /&gt;
&lt;/PublishProfile&gt;</pre>

Add the new profile file to the project in Visual Studio.

Right click the project and select &#8220;Publish&#8221;. In the Publish dialog, select your new Profile file from the first dropdown. The dialog will verify it can connect to the Cluster:

<div id="attachment_8723" style="width: 648px" class="wp-caption aligncenter">
  <img src="http://blogs.ltd.local/wp-content/uploads/2017/07/PublishPackage.png" alt="VS 2017 - Publish Package for Service Fabric" width="638" height="434" class="size-full wp-image-8723" srcset="http://blogs.ltd.local/wp-content/uploads/2017/07/PublishPackage.png 638w, http://blogs.ltd.local/wp-content/uploads/2017/07/PublishPackage-300x204.png 300w" sizes="(max-width: 638px) 100vw, 638px" />
  
  <p class="wp-caption-text">
    VS 2017 &#8211; Publish Package for Service Fabric
  </p>
</div>

(Yes, I&#8217;m using a hotmail address, it amuses me 🙂 )

Click Publish and Visual Studio will build the project and publish it to the cluster. Visual Studio will provide feedback as it publishes the application and we can see the results in the dashboard:

<div id="attachment_8724" style="width: 1034px" class="wp-caption aligncenter">
  <img src="http://blogs.ltd.local/wp-content/uploads/2017/07/UnhealthyDeployedPackage-1024x879.png" alt="Unhealthy, But Deployed Dashboard View" width="1024" height="879" class="size-large wp-image-8724" srcset="http://blogs.ltd.local/wp-content/uploads/2017/07/UnhealthyDeployedPackage-1024x879.png 1024w, http://blogs.ltd.local/wp-content/uploads/2017/07/UnhealthyDeployedPackage-300x258.png 300w, http://blogs.ltd.local/wp-content/uploads/2017/07/UnhealthyDeployedPackage-768x660.png 768w, http://blogs.ltd.local/wp-content/uploads/2017/07/UnhealthyDeployedPackage.png 1345w" sizes="(max-width: 1024px) 100vw, 1024px" />
  
  <p class="wp-caption-text">
    Unhealthy, But Deployed Dashboard View
  </p>
</div>

Successful deployment! Except my application is unhealthy in this case, which I will now go start to debug 🙂

 [1]: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-for-windows-server#downloadpackage
 [2]: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-manifest
 [3]: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-manifest#nodetypes
 [4]: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security
 [5]: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security#install-the-certificates
 [6]: https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-standalone-deployment-preparation#step-7-environment-setup