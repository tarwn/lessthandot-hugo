---
title: Real World Azure – Queue PopReceiptMismatch Bug
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-09-09T12:41:00+00:00
ID: 2151
excerpt: |
  This week I'm starting a new series on "Real World Azure". These are stories or issues I have run into while working with Azure in the "Real World". Today we're looking at a bug in the Azure API for Queue Services that appears to have been around for at&hellip;
url: /index.php/desktopdev/mstech/real-world-azure-queue-popreceiptmismatch/
views:
  - 32816
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - azure queue service
  - real world azure
  - unit testing
  - windows azure

---
This week I'm starting a new series on “Real World Azure”. These are stories or issues I have run into while working with Azure in the “Real World”. Today we're looking at a bug in the Azure API for Queue Services that appears to have been around for at least the last two versions of the storage API (prior to 2011-08-18, or SDK 1.7).

<div style="background-color: #ffff99; padding: .5em; margin: 1em;">
  <h2 style="margin: .5em 0px;">
    Real World Azure
  </h2>
  
  <p>
    There are a lot of great resources out there on Azure, from demos to webcasts to white papers filled with architectural diagrams. This is to be expected. Microsoft products tend to focus on the 15 minute demo or polished architecture diagram in an enterprise whitepaper, a controlled exposure of only a subset of the functionality you will use in the real world.
  </p>
  
  <p>
    I have used Azure daily for years on live business and personal projects, not demos. From supporting production systems running hundred of millions of storage transactions to figuring out why a change to the Azure Management API limits sends certain legacy code into a death spiral to working directly with the APIs in 3-4 different languages to months where we had 2-4 active support cases at any time. These are examples found in the real, production usage.
  </p>
</div>

Today we're looking at a longstanding bug in the [Azure Queue Service][1] that I've been struggling with lately. 

## What is the Azure Queue Service?

The Queue Service allows you to create Queues and Put and Get messages from those queues. Queues in the Queue Service are equally accessible from any number of other Azure services or sites, making it an excellent mechanism for communicating between services.

The Queue uses a two-phase Get/Delete process for dequeueing Messages. When you Get the item, it goes invisible for a period of time you specify until you have finished and call Delete. If you have network problems, a system crash, or other unforeseen circumstances, the item resurfaces in it's original position, allowing another of your resources to pick it up and finish executing it instead of losing it forever. <div style="text-align: center; color: #666666; margin: .5em""> 

![][2]
     
GETting a Message makes it invisible, DELETEing removes it </div> 

An Update method provides the ability to resurface the Message, extend the visibility timeout, or update the contents of the Message. This last option shows up in Microsoft examples where they have multi-phase work, so when a Message resurfaces you can pick the work up at the last step it reached instead of starting from the beginning (not my recommendation, but that's another topic).

The ability to break down work into atomic units and then spin up N units consuming items from that queue is a powerful tool for horizontally scaling work.

## The PopReceipt &#8211; Not Just for GetMessage Calls

To prevent other processes from updating a Message that has been Get-ed, a unique PopReceipt is issued with the GetMessage response. Later UpdateMessage and DeleteMessage calls are required to have this PopReceipt to access the item. This prevents crosstalk or a Message resurfacing and being picked up by a second worker, then being updated or deleted by the original worker. Very handy.

But wait, I thought this was all to resolve cases where the first system has gone down, why would it try to update that Message again if it's down or rebooted?

Setting aside the case where you were persisting the Message Id and PopReceipt somewhere that is accessible when your system un-crashes (don't do that), there is one other catch to all of this. Every time you call UpdateMessage to extend the visibility timeout or update the content, a new PopReceipt is issued. This means that in order to continue processing it and complete (Update and Delete) you have to have perfect tracking of the latest PopReceipt throughout the lifetime of the job. 

So when you have a network error (which is the subject of another post and numerous support tickets) and don't receive the response to a successful UpdateMessage, you lose the last PopReceipt, even if you can continue and successfully complete processing and only lost one Update call out of 100 during the lifetime of a long-lived Message.

## The PopReceiptMismatch Error

The Azure REST API outlines all of the errors you can expect to get back, nicely broken down into a [common set of errors][3] and service-specific lists ([Queue Service Errors][4]).

The error code we are looking at is:

<div style="background-color: #EEEEEE; margin: .5em; padding: .5em">
  PopReceiptMismatch: Bad Request (400) &#8211; The specified pop receipt did not match the pop receipt for a dequeued message.
</div>

The [Delete Message][5] (Last updated Sept, 2011) documentation specifically outlines this scenario:

> If a message with a matching pop receipt is not found, the service returns status code 400 (Bad Request), with additional error information indicating that the cause of the failure was a mismatched pop receipt.

The [Update Message][6] (Last updated Sept, 2011) documentation is less specific but sounds like we should expect the same error (emphasis mine):

> An Update Message operation will fail if the specified message does not exist in the queue, or if the specified **pop receipt does not match** 

So it makes sense that if we GetMessage and somehow have an out of date PopReceipt or attempt to use a PopReceipt that belongs to a different item, we would receive a PopReceiptMismatch error.

**Actual Response:**

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error>
   <Code>MessageNotFound</Code>
   <Message>The specified message does not exist.
RequestId:98bd9fb1-d32f-45bd-9159-c900a9b2fed3
Time:2013-09-07T17:04:30.5469796Z</Message>
</Error>
```
Wait, what? 404, MessageNotFound, “The specified message does not exist.”? 

That doesn't match the documentation OR sound correct?

## The PopReceiptMismatch Bug

I've written a series of unit tests that show that you receive “Item not found” from using an outdated PopReceipt, a PopReceipt from another queue's item, and a fabricated PopReceipt. All cases where I would expect to receive the PopReceiptMismatch error.

[AzureQueueIssues/PopReceiptMismatchReturnsWrongError.cs][7] (Full code available on Github)

```csharp
[Test]
public void UpdateMessage_UsingIncorrectPopReceipt_Returns400PopReceiptMismatch()
{
	// Create the queue client
	var queueClient = _account.CreateCloudQueueClient();

	// Retrieve a reference to a queue
	var queue = queueClient.GetQueueReference("unit-test" + Guid.NewGuid().ToString());
	queue.CreateIfNotExists();
	queue.Clear();

	// let's queue up a sample message
	var message = new CloudQueueMessage("test content");
	// note the unecessary difference in terminology between API (Put) and reference SDK (Add)
	queue.AddMessage(message);

	// now we get the item and violate the first Get's popreceipt
	var queueMessage1 = queue.GetMessage(TimeSpan.FromSeconds(1));
	Thread.Sleep(TimeSpan.FromSeconds(2));
	var queueMessage2 = queue.GetMessage(TimeSpan.FromSeconds(60));
	// and just to be absolutely clear that we didn't get the same receipt a second time
	Assert.AreNotEqual(queueMessage1.PopReceipt, queueMessage2.PopReceipt);

	// now lets harvest the error from using the first popreceipt
	int statusCode = -1;
	string status = "not defined";
	try
	{
		queue.UpdateMessage(queueMessage1, TimeSpan.FromSeconds(60), Microsoft.WindowsAzure.Storage.Queue.MessageUpdateFields.Visibility);
	}
	catch (StorageException exc)
	{
		statusCode = exc.RequestInformation.HttpStatusCode;
		status = exc.RequestInformation.HttpStatusMessage;
	}

	// prove that the item is still valid and it was definately a popreceipt mismatch
	queue.UpdateMessage(queueMessage2, TimeSpan.FromSeconds(60), Microsoft.WindowsAzure.Storage.Queue.MessageUpdateFields.Visibility);

	//cleanup
	queue.Delete();

	// documented response
	Assert.AreEqual(Error_PopReceiptMismatchMessage, status);
	Assert.AreEqual(ErrorCode_PopReceiptMismatch, statusCode);
}
```
By getting a Message, allowing it's visibility timeout to expire, and getting it a second time, we can ensure the original popreceipt is no longer valid.

(and that I can't spell definitely without spellcheck)

In a [forum post from 2011][8] a microsoft representative said that the PopReceiptMismatch error is not supposed to be returned for all Pop Receipt Mismatches, only when you use a PopReceipt from another message/queue. His answer indicated that this was an error in the documentation (common response on the forums: service works right the way it is, the documentation must be incorrect). 

The cross-queue case is of course included in a test like the one above and available on Github. Spoiler: 404 Message does not exist.

**Conclusion:** When a Message does not exist or it exists but you have the wrong PopReceiptMismatch, you will receive “The specified message does not exist.”

Here are the tests that I ran against live Azure and the latest emulator:

UpdateMessage\_UsingIncorrectPopReceipt\_Returns400PopReceiptMismatch
:   Fails, receives 404, Message does not exist

UpdateMessage\_UsingHandWrittenCode\_Returns204NoContentOnSuccesfulUpdate
:   Success, verifies code to consume API works for use in later tests

UpdateMessage\_UsingIncorrectPopReceiptFormat\_ReturnsInvalidParameterAndNotPopReceiptMismatch
:   Success, the Common Service Errors documentation is correct, this does not return PopReceiptMismatch as was outlined in the 2011 forum post

UpdateMessage\_UsingMadeUpButValidFormatPopReceipt\_Returns400PopReceiptMismatch
:   Fails, receives 404, Message does not exist

So either this is a bug in the Azure Service code, which continued through at least 2 later versions of the API and at least two versions of the Storage Emulator, or this was intended. Given the granularity and number of service errors defined throughout the rest of the service, the first case is more likely. 

As an additional level of confirmation, during a recent Azure support case, the person helping me was sidetracked off into other investigations because he also expected us to be receiving (400) PopReceiptMismatch for this situation. Even the Storage Analytics logs reflects this as a 404.

## The Workaround

There isn't one. 

Unfortunately there is no method we can call to determine if the Message actually still exists or not.

The two resolutions I have considered require an absurd amount of extra development. One way would be to build a proxy or logging layer that logs every single call we make, centrally, and tracks the latest state every message is in per our calls out to the service. The next option would be to consume the Analytics logs and track the latest status available even if the individual HTTP call ran into an error during reception. Both of which seem like as much work as building our own queue service.

## Questions

There are several things it would be nice to know:

**1) Confirmation of this bug as well as an indication of when it will be fixed.** 
  
Unfortunately it is entirely possible someone has built logic that relies on the incorrect error message (and how they tell which case it is, I have no idea), so I suspect an API version would be required to fix it safely.

**2) Why hasn't this been uncovered in Microsoft's integration testing on the service?** 
  
I have a custom SDK that consumes the API. One responsibility of this library is to map received errors into specific custom Exception types (I find these far easier to use then having one StorageException with codes, like the reference SDK from Microsoft). To build automated test cases for this library, I copied the Error Code tables from the documentation and made TestCase attributes with search/replace and some regex magic. A similar method could be used for Integration tests. Add in a tool like [FitNesse][9], and their MSDN documentation could serve as the list of test cases automatically.

**3) Why does the PopReceipt change on Updates?**
  
Why do the PopReceipts change on Updates at all? I can't think of a single valid case where this is useful. In fact this smells an awful lot like a developer taking a shortcut while working on the Azure service and calling or re-using logic from GetMessage and getting this extra behavior by accident. I haven't been able to find an answer to this question yet.

More Real World Azure to come. Technical issues, how to work with support and their limitations, my personal known issues list, and so on.

 [1]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/queue-service/ "Azure Queue Service on WindowsAzure.com"
 [2]: http://tiernok.com/LTDBlog/RealWorldAzure/TwoStepDequeue.png
 [3]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179357.aspx
 [4]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
 [5]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179347.aspx
 [6]: http://msdn.microsoft.com/en-us/library/windowsazure/hh452234.aspx
 [7]: https://github.com/tarwn/AzureQueueIssues/blob/master/PopReceiptMismatchReturnsWrongError.cs
 [8]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/aab37e27-2f04-47db-9e1d-66fd224ac925/handling-queue-message-deletion-error
 [9]: http://fitnesse.org/