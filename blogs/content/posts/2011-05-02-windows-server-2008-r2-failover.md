---
title: 'Windows Server 2008 R2 Failover Clustering: It’s Cool'
author: Jes Borland
type: post
date: 2011-05-02T11:43:00+00:00
excerpt: "I recently took a three-day class on Windows Server 2008 R2: Deploying and Managing Failover Clustering. Here's an overview of some new features, and what I learned."
url: /index.php/datamgmt/dbadmin/windows-server-2008-r2-failover/
views:
  - 8593
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
I recently took a three-day class on [Windows Server 2008 R2: Deploying and Managing Failover Clustering][1]. 

<div class="image_block">
  <a href="/wp-content/uploads/users/grrlgeek/blackboardteacher.JPG?mtime=1304295364"><img alt="" src="/wp-content/uploads/users/grrlgeek/blackboardteacher.JPG?mtime=1304295364" width="309" height="256" title="Class Is In Session" /></a>
</div>

My clustering experience: At my previous job, I had supported SQL Server 2005 on a Windows Server 2003 cluster for a couple of years. I had tried to install a Windows Server 2008 cluster last year (and failed miserably, because I didn’t know anything). With my current job, I support several instances of SQL Server 2008 on Windows Server 2008 and SQL Server 2005 on Windows Server 2003. 

What I knew about clusters: I could fail over a cluster resource and make sure it came back online. I understood the concept of nodes, quorum and shared disk. That was about it. I don’t think I had any idea how much I didn’t know until I took this class. 

Here are a few of the things I learned about clustering, and specifically things that are new to Windows Server 2008 R2 Failover Clustering. 

First, there is a big difference between someone who knows a lot about a subject and can teach it, and a good teacher. The instructor, while extremely knowledgeable, was a little dry and I don’t feel he managed to keep the entire class engaged. The format was lecture, then lab. I felt he could have walked through the labs with us, answering questions as went, rather than just letting us go on our own. As a speaker, I learned a lot from him, and will always strive to be a good teacher. 

Second, the capabilities of PowerShell stood out in this class. 

The basics of clustering: it’s a high-availability technology. Clustering involves setting up nodes that communicate with each other. They share storage. If one node goes down, it fails over to another on the cluster (if all goes according to plan!). Here’s what else I learned. 

**Quorum** 

In 2003, there were three types of quorum: shared disk, majority node set and local. My environment uses all shared disk. 2008 R2 has evolved. The options available now are node majority, node and disk majority, node and file share majority and disk only. 

_Node majority_ – default for an odd number of nodes. Used when there are an odd number of nodes and no shared storage. 

_Node and disk majority_ – default for an even number of nodes and a witness disk. Cluster configuration information is stored on each node. 

_Node and file share majority_ – rather than a witness, a shared file resource – generally at a different geographic location – is the additional vote in quorum. My environment uses this, as we have a geographic cluster set up using three data centers. 

_Disk only_ – even number of nodes with a shared disk (witness). The equivalent of shared disk in 2003. 

**Storage** 

Parallel SCSI is no longer supported for shared disks. SAS, iSCSI and Fibre Channel are supported. 

Both MBR (master boot record) and GPT (GUID Partition Table) are supported. GPT is cool because a disk can now be partitioned beyond 2TB. A year ago, I would have giggled at the thought of ever needing that much space in one disk. Now? I work with single databases approaching 1.3 TB. Microsoft provides a good FAQ on GPT [here][2]. 

**Networking** 

You can now set up a multi-subnet cluster. The cluster resources are set up as AND/OR dependencies. 

**Cluster shared volumes** 

This is cool. If you have a cluster that is a series of Hyper-V virtual machines, you can have these nodes concurrently access, and modify, files on one LUN. Cool, right? The volume appears in C:ClusterStorage on each node. More information can be found here: http://technet.microsoft.com/en-us/library/dd630633(WS.10).aspx. 

**DFS FTW** 

Until this class, I had not heard of DFS (Distributed File System). Because of that, I hadn’t heard of DFS Replication. What was I missing? A lot! DFS is, if I understand it correctly, a way to logically organize a series of folders on multiple disks, which users see as a series of folders and subfolders. Add in DFS Replication and it takes it up a level. It allows you to keep a set of folders in sync across multiple servers, using replication and clustering. 

**Paxos** 

For the first time, I looked at the cluster keys in the registry. One of the most important has the coolest name: Paxos. It’s a tag that ensures consistency on all nodes. It’s in the format: 3:3:157. 

These are the NextEpoch Number, LastEpoch Number, and Sequence Number. All nodes should have the same tag in the registry. Any time a change is made to the cluster configuration, the sequence number is increased. 

**Security Changes** 

There’s been a big change for cluster security. The cluster service account is no longer a domain user account, but is a local system account. This can prevent changes to a domain account, even if they were accidental, from adversely impacting the cluster. 

The default authentication method in clusters has also changed to Kerberos. NTLM authentication will still be usable, if necessary. 

**MSDTC Changes** 

Previously, one DTC resource was configured on a cluster and all applications would use it. In 2008 R2, though, multiple DTC resources can be configured. Then, an application running in a resource group can be set up to use a specific DTC resource. This allows for more efficient resource management. 

**Backup/Restore** 

I learned the difference between the two types of restore available with a cluster. 

_Non-Authoritative_ – This is a full restore of a cluster node. When the node is restored, it is set to use the current configuration of the cluster (using that Paxos tag!). 

_Authoritative_ – This restores a cluster back to a point in time. One node is restored, then the other nodes are brought back into the configuration and updated with the correct Paxos tag. 

**The Migration Wizard** 

Even though it lacks a pointy hat and a wand, the migration wizard is really handy. It takes cluster resources running on Windows Server 2003, 2008 or 2008 R2, and can migrate them to 2008 R2. Only resource groups, highly available services or applications can be moved individually. There is even a pre-migration report to help you understand if your resources can be moved. 

**Labs** 

I was able to walk through an installation of a two-node cluster using both the wizard on a standard install, and PowerShell on a Core install. 

If you have done all your preparation correctly, the installation should be painless. In a standard Windows Server 2008 R2 setup, the Failover Clustering feature is installed on all nodes, and the wizard walks you through selecting the nodes that will be part of the cluster. A validation report is run. If it passes all tests, you create your cluster. 

On a Windows Server 2008 R2 Core, on both nodes you configure the shared storage, PowerShell and Remote Management. You install Failover Clustering. Then, with one line of PowerShell, I created the cluster. Yes, it was that easy. 

<p align="center">
  <img src="http://s7d5.scene7.com/is/image/Staples/s0105150_sc7?$sku$" height="125" width="165" />
</p>

**Take-Away** 

I will probably not need to research, prepare and install a cluster from scratch anytime in the near future – I have an entire server team that takes care of that. However, I have a much better understanding of clustering in general now. I will be able to troubleshoot problems with clusters better. I know my way around Failover Cluster Manager. I could add a resource. I can use PowerShell commands to easily check settings. I can even create a Hyper V machine. All in all, it was a great class and I’m glad I took it.

 [1]: http://www.google.com/url?sa=t&source=web&cd=1&ved=0CBYQFjAA&url=http%3A%2F%2Fdownload.microsoft.com%2Fdocuments%2Faustralia%2Fservices%2Fdatasheets%2FWindows_Server_2008_R2_Deploying_and_Managing_Failover_Cluster_WorkshopPLUS(4Days).pdf&ei=ekm8TfKdJtGQtgeI8JiPBA&usg=AFQjCNFfCz9b2ZWH2vfUDWO4a9TSMw-iHA
 [2]: http://support.microsoft.com/kb/302873