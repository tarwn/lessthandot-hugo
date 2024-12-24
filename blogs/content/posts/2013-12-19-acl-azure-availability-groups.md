---
title: ACL added security for Availability Group Listener in Azure
author: Ted Krueger (onpnt)
type: post
date: 2013-12-19T21:29:00+00:00
ID: 2212
excerpt: 'Prior to the release of supporting listeners for availability groups in Azure running Windows Server virtual machines, availability groups were supported but more so in a mirroring configuration.  This means, applications, services, or users would conne&hellip;'
url: /index.php/datamgmt/dbadmin/acl-azure-availability-groups/
views:
  - 20136
rp4wp_auto_linked:
  - 1
categories:
  - Azure
  - Database Administration
  - Microsoft SQL Server Admin

---
Prior to the [release of supporting listeners][1] for availability groups in Azure running Windows Server virtual machines, availability groups were supported but more so in a mirroring configuration.  This means, applications, services, or users would connect to the instance name and in the event of a failover, the name would have to change.  Availability groups power lies in the concept that there is a complete protection level thanks to clustering and single entry points.  Alleviating the need for custom or addition tasks to be performed in the case data, hardware or operating system failures occur.

Now that listeners are supported and a [fairly straight forward setup][2] has been released to get the listener going, there is one area that still needs to be addressed – instance exposure.  Exposure meaning, the way listeners are now supported is managed by taking advantage of direct server return from one cloud service to another.  This means, a path is taken to access the listener by name or IP and then is redirected outside the cloud service, and back in through the public cloud service's IP address.  This method of having an entry point through the public IP address leaves more need for security considerations.  This will be evident in the case of a configuration that may have an on premises infrastructure accessing an Azure infrastructure through a VPN tunnel, which is a secure method of accessing the cloud service.  However, while the listener may reside within the walls of this topology, the implementation of a load balanced endpoint on the virtual machines hosting the SQL Server instance, then a direct server return to redirect out and back using the public IP, exposes the instance to anyone knowing the IP address.  For example, if you have the following setup

<div class="image_block" align="center">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/AG listener in Azure.gif?mtime=1387488441"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt/AG listener in Azure.gif?mtime=1387488441" width="996" height="936" /></a>
</div>

In the case of the above setup, if you leave everything configured with the means of the listener as it is, given the provided setup documentation, you would be fully capable of using SSMS from any remote address and attempting to connect to the SQL Server instance within the Azure virtual machine.  Now, security and port configurations may prevent you from logging in but, with a little work, we all know that things exposed are things available to be broken into.

The way we can secure this further is to use access control lists (ACL) on the load balanced endpoint in Azure.  ACLs are not new to Azure and are used on regular endpoints often.  The ACL will allow access to the load balanced endpoint to be allowed only from a range or specific IP address.  Although, spoofing IP addresses is still a risk, this adds a much needed layer of security to an otherwise, left open entry point of a SQL Server instance on an Azure VM.

While it may be difficult to determine how to implement an ACL for a load balanced endpoint, PowerShell for Azure makes the task fairly simple by using the [Set-AzureLoadBalancedEndpoint][3] and [Set-AzureAclConfig][4] commands.   Between these two commands, the configuration is a matter of determining the IP or IP range that you want to allow access, the cloud service name, and the load balanced endpoint name.

For example, referring back to the diagram above, if the following names are associated with the objects needed to configure an ACL for a network range of only 189.123.0.0/16

  * Cloud Service – SQLAzureService
  * Load Balanced Endpoint – SQLAGEndpoint

In PowerShell for Azure, we would construct the following

```
$ApplicationCloudServiceIPsubnet = "189.123.0.0/16"
$ServiceName = "SQLAzureService"
$LBSetName = "SQLAGEndpoint"
$acl = New-AzureAclConfig
Set-AzureAclConfig –AddRule –ACL $acl –Order 5 –Action Permit –RemoteSubnet $ApplicationCloudServiceIPsubnet –Description "ACL to SQLAGEndpoint "
Set-AzureLoadBalancedEndpoint –ServiceName $ServiceName –LBSetName $LBSetName -Protocol tcp –LocalPort 1433 –PublicPort 1433 –ProbePort 99999 -ProbeProtocolTCP -DirectServerReturn $true –ACL $acl
```

Granted, the above is allowing direct access to SQL Server over port 1433.  This may not be typical and further changes are typical to utilize a port that isn't the default port listening by SQL Server. However, a port scan quickly shows these listening ports so the addition of requiring connections to specify ports may not be viable or capable of the connecting services.  With an ACL now implemented, direct remote access to the SQL Server instance from any IP address other than within the range of 189.123.0.0/16 would not be allowed through.

 [1]: http://blogs.technet.com/b/dataplatforminsider/archive/2013/08/12/alwayson-availability-groups-fully-supported-on-windows-azure-infrastructure-services.aspx
 [2]: http://msdn.microsoft.com/en-us/library/windowsazure/dn376546.aspx
 [3]: http://msdn.microsoft.com/en-us/library/dn408486.aspx
 [4]: http://msdn.microsoft.com/en-us/library/dn408561.aspx