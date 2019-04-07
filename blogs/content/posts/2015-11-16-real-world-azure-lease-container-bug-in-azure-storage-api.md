---
title: 'Real World Azure: Lease Container bug in Azure Storage API'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2015-11-16T14:10:14+00:00
url: /index.php/enterprisedev/cloud/azure/real-world-azure-lease-container-bug-in-azure-storage-api/
views:
  - 2689
rp4wp_auto_linked:
  - 1
categories:
  - Azure
tags:
  - azure
  - real world azure

---
Recently we&#8217;ve been working with the raw Azure Storage API to try and get to a more stable solution then the far more aggressively changing Azure Storage SDK. One of the goals is to be able to work equally well locally, against the emulator, and in production. We&#8217;re used to cases where the Emulator diverges from production or the documentation, but recently we found a case where the emulator and documentation match, but the production services appear to be wrong.

<div style="background-color: #ffff99; padding: .5em; margin: 1em;">
  <h2 style="margin: .5em 0px;">
    Real World Azure
  </h2>
  
  <p>
    There are a lot of great resources out there on Azure, from demos to webcasts to white papers filled with architectural diagrams. This is to be expected. Microsoft products tend to focus on the 15 minute demo or polished architecture diagram in an enterprise whitepaper, a controlled exposure of only a subset of the functionality you will use in the real world.
  </p>
  
  <p>
    I have used Azure daily for years on live business and personal projects, not demos. From supporting production systems running hundred of millions of storage transactions to figuring out why a change to the Azure Management API limits sends certain legacy code into a death spiral to working directly with the APIs in 3-4 different languages to months where we had 2-4 active support cases at any time. These are examples found in the real, production world.
  </p>
</div>

The prior &#8220;real world azure&#8221; post (September 2013) was a [Azure API Queue bug][1] that is still present today. 

This newer bug is more minor, unless you are relying on the error codes to be correct, in which case it&#8217;s kind of painful. It&#8217;s also concerning because, while we don&#8217;t build Storage APIs and SDKs for a living, we caught this in our integration tests relatively quickly, but it appears to have been missed in Microsoft testing thus far.

# What is Azure Blob Storage?

The shortest explanation I can provide for Azure Blob service is to think of it as an infinitely wide file system. Azure blobs reside in Containers (folders). We have the ability to Lease Containers or Blobs (think of leases as similar to file locks that have the option of automatically releasing at a future time). Once a Container or Blob is Leased, only operations that include the correct lease are allowed to operate on them (except some cases where having no lease is still allowed, like read/download).

# Leasing Non-Existent Containers</h2> 

The Azure REST API outlines all of the errors you can expect to get back, nicely broken down into a common set of errors and service-specific lists ([Blob Service Errors][2]).

The two error codes we are looking at are:

> ContainerNotFound: Not Found (404) &#8211; The specified container does not exist.
  
> BlobNotFound: Not Found (404) &#8211; The specified blob does not exist. 

A test for the [Lease Container Operation][3] can be implemented using the SDK like this:

[ContainerNotFoundReturnsWrongError.cs][4]

<pre>/// <summary&gt;
/// Emulator: Returns 404 Container Not Found (tested with 3.3 and other versions)
/// Azure API: Returns 404 Blob Not Found (tested with 3.3 and other versions)
/// </summary&gt;
[Test]
public void AcquireLease_NonExistentContainer_ReturnsContainerNotFoundError()
{
    var blobClient = _account.CreateCloudBlobClient();
    var containerReference = blobClient.GetContainerReference("nonexistent-container");

    int statusCode = -1;
    string status = "not defined";
    try
    {
        containerReference.AcquireLease(TimeSpan.FromSeconds(15), Guid.NewGuid().ToString());
    }
    catch (StorageException exc)
    {
        statusCode = exc.RequestInformation.HttpStatusCode;
        status = exc.RequestInformation.HttpStatusMessage;
    }

    Assert.AreEqual(ErrorCode_NotFound, statusCode);
    Assert.AreEqual(ErrorStatus_ContainerNotFound, status);
}</pre>

_(There are also examples of raw HTTP implementations in that same test file to verify it is not an SDK error, which is also why we&#8217;ll look at the response at the network level using fiddler)._

On the local emulator, this will return the following details (fiddler):

[<img src="/wp-content/uploads/2015/11/EmulatorFiddler.png" alt="LeaseContainer - local Emulator response (Fiddler)" width="322" height="190" class="aligncenter size-full wp-image-4253" srcset="/wp-content/uploads/2015/11/EmulatorFiddler.png 322w, /wp-content/uploads/2015/11/EmulatorFiddler-300x177.png 300w" sizes="(max-width: 322px) 100vw, 322px" />][5]

Against a production API, it returns the following details (fiddler):

[<img src="/wp-content/uploads/2015/11/LiveAzureFiddler.png" alt="LeaseContainer - Live Azure Response (Fiddler)" width="342" height="190" class="aligncenter size-full wp-image-4254" srcset="/wp-content/uploads/2015/11/LiveAzureFiddler.png 342w, /wp-content/uploads/2015/11/LiveAzureFiddler-300x166.png 300w" sizes="(max-width: 342px) 100vw, 342px" />][6]

In this case, the emulator is correct, but the production Storage API returns the wrong error.

I tested this against multiple versions of the API, locally and in the cloud, and got the same results: the production Storage API returns the wrong error code for LeaseContainer operations.

 [1]: /index.php/desktopdev/mstech/real-world-azure-queue-popreceiptmismatch/
 [2]: https://msdn.microsoft.com/en-us/library/azure/dd179439.aspx "MSDN: Blob Service Errors"
 [3]: https://msdn.microsoft.com/en-us/library/azure/jj159103.aspx "MSDN: LeaseContainer Operation"
 [4]: https://github.com/tarwn/AzureQueueIssues/blob/master/ContainerNotFoundReturnsWrongError.cs "ContainerNotFoundReturnsWrongError from tarwn/AzureQueueIssues on github"
 [5]: /wp-content/uploads/2015/11/EmulatorFiddler.png
 [6]: /wp-content/uploads/2015/11/LiveAzureFiddler.png