---
title: Azure Storage SDK 3.0.2 and Preview Azure Storage Emulator 2.2.1
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-02-17T16:35:56+00:00
ID: 2401
url: /index.php/enterprisedev/cloud/azure/azure-storage-sdk-3-0-2-and-preview-azure-storage-emulator-2-2-1/
views:
  - 9858
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - azure

---
Late in November, the Azure Storage team announced an update of the Storage Library that corresponds with the “2013-08-15” Storage API update. They weren't explicit about it in the release post, but this was a major version upgrade to Azure Storage SDK 3.0. The 3.0.2 Storage SDK is not part of an official Azure SDK release, the Azure 2.2 SDK officially shipped with Azure Storage 2.1.0.2 ([release notes][1]). I expect the next Azure SDK will include this Storage SDK (or perhaps a 3.1 version, depending on timing).

_[Read the SDK release announcement here, 27 Nov 2013][2]_

<div style="background-color: #eeeeee; padding: .5em">
  <strong>A Plethora of Version Numbers</strong></p> 
  
  <p>
    The Azure SDK is composed of a number of different assemblies and projects that are versioned independently. The Azure API has been updated annually for the last few years, the latest version being “2013-08-15”, a yyyy-MM-dd value. The Azure Storage SDK for .Net split from the main versioning between SDK 1.7 and 1.8, going from 1.7 to 2.0. The Azure ConfigurationManager assembly is currently on 2.0.2.11-something, is listed in nuget as 2.0.3, has a file version of 2.0.0.0, and is part of Azure SDK 2.2. I haven't looked at the node, java, etc projects and have not looked terribly closely at the the rest of the assemblies that make up the .Net Azure SDK.
  </p>
  
  <p>
    I'll try to be clear which thing a version applies to as the post progresses.
  </p>
</div>

Besides the confusion of mismatched version numbers, this version of the Storage SDK was released without local development (emulator) support. When released, there was no emulator support for “2013-08-15”, however a [preview version of the emulator][3] has been released now, for those of us that depend on local development.

## Azure Storage SDK 1.7 (Silently?) Deprecated

The 1.7 version of the sdk has been silently deprecated. I initially thought I missed the announcement in one of the SDK release posts, but I have been unable to find any news on this via web searches. But if you look in the right places in [MSDN documentation][4]:

<div id="attachment_2408" style="width: 710px" class="wp-caption aligncenter">
  <a href="/wp-content/uploads/2014/02/AzureSDK1.7Deprecated.png"><img src="/wp-content/uploads/2014/02/AzureSDK1.7Deprecated.png" alt="MSDN Documents 1.7 as Deprecated, 3.0 as Recommended" width="700" height="187" class="size-full wp-image-2408" srcset="/wp-content/uploads/2014/02/AzureSDK1.7Deprecated.png 700w, /wp-content/uploads/2014/02/AzureSDK1.7Deprecated-300x80.png 300w" sizes="(max-width: 700px) 100vw, 700px" /></a>
  
  <p class="wp-caption-text">
    MSDN Documents 1.7 as Deprecated, 3.0 as Recommended
  </p>
</div>

There you have it, documented as deprecated and obviously updated some time in the few months to reflect 3.0.

## Links

The preview version of the emulator can be found here: [Windows Azure Storage Emulator 2.2.1 Preview][3] 

The breaking changes from Storage SDK 2.1 and 2.2 to 3.0.2 are available here: [3.0.2 Breaking Changes][5]

The source code for the Storage SDK has moved. Perhaps to one-up the folder restructures that have happened a couple times in the past, they completely moved the storage code from [WindowsAzure/azure-sdk-for-net][6] to [WindowsAzure/azure-storage-net][7]. The older repository has branches for 1.7, 1.7.1, and 2.0 and tags for 5 flavors of 2.1 (storage sdk? azure sdk?) among others. The new one appears to just be 3.0.2 and, at the time of this writing, seems to be missing a lot of history and all the unit tests.

The MSDN documentation that indicates 1.7 is deprecated can be found here: [Storage Client Library (June 2012 and earlier)][4]

## Opportunities to Improve

I'm glad Microsoft is releasing more frequently instead of the 2-3+ year gaps it used to take, but I'd like to see them pay more attention to the impact these releases are having on customers.

The move from 1.7 to 2.0 dropped a number of synchronous methods, renamed the namespace, and shipped with the 1.8 Azure SDK which still required the 1.7 version of Storage for the diagnostics library. Right up until 2.0 sprang into the world (literally days prior), developers were still telling us that 1.7.1 was the future and we should be using that for some of the new features (feel free to compare [1.7.1][8] and <a href="https://github.com/WindowsAzure/azure-sdk-for-net/tree/sdk-2.0/microsoft-azure-api/Services/Storage title="2.0 Storage Branch on github">2.0</a> on github). 

I'm also still waiting to see the Storage SDK become friendly to unit testing, between the sealed classes, internal methods, and lack of abstractions, it still requires you to write your own storage abstraction layer on top of their storage abstraction in order to support unit testing.

Subsequent releases haven't been that bad (after 1.7 to 2.0), but now we have another major version release, this time completely out of step with Azure releases and announced without even announcing the fact that it was a major version upgrade. 

Updated versions of the storage emulator trail months behind the release, ensuring slow uptake of new versions or adding additional time and dollar expense by forcing customers to develop directly against real Azure Storage Services accounts.

And the removal of the unit tests doesn't fill me with a high level of confidence either. I didn't particularly like the style of the older tests, but at least they were there.

So here we are, at another major release and a silent deprecation of 1.7. I've personally wasted a lot of time upgrading from 1.7 to 1.7.1, then throwing that work out when 1.7.1 remained a preview (with all the extra conversation that causes in support cases) and 2.0 didn't look anything at all like 1.7.1, upgrading to 2.0 and filling in the holes they decided to add, then backing it all out again due to other conflicts and the fact that none of this has any value at all for our customers and...well, you get the picture.

A huge part of the value in releasing more frequently is bringing the pain to the surface _and then fixing it_. It seems the frequent release half of the equation has happened, now I'm looking forward to the part where they start attacking these pains.

 [1]: http://msdn.microsoft.com/en-us/library/windowsazure/dn459835.aspx
 [2]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/11/27/windows-azure-storage-release-introducing-cors-json-minute-metrics-and-more.aspx
 [3]: http://www.microsoft.com/en-us/download/details.aspx?id=41670
 [4]: http://msdn.microsoft.com/en-us/library/wa_storage_mref_reference_home.aspx
 [5]: https://github.com/WindowsAzure/azure-storage-net/blob/ffdb2ebaeede29449afb810bc94e4bb9224c9ca3/BreakingChanges.txt "3.0.2 Breaking Changes on github"
 [6]: https://github.com/WindowsAzure/azure-sdk-for-net "WindowsAzure/azure-sdk-for-net on github"
 [7]: https://github.com/WindowsAzure/azure-storage-net "WindowsAzure/azure-storage-net on github"
 [8]: https://github.com/WindowsAzure/azure-sdk-for-net/tree/sdk_1.7.1/microsoft-azure-api