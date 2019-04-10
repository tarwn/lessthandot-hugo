---
title: Copying Buckets With The Amazon S3 API
author: Alex Ullrich
type: post
date: 2012-05-29T14:43:00+00:00
ID: 1629
excerpt: "One of the projects I have been working on is a system for managing content on our network of websites.  One of our requirements is that changes don't take effect immediately, but on a separate preview network where our customer can look to see that her&hellip;"
url: /index.php/webdev/serverprogramming/copying-buckets-with-the-amazon-s3-api/
views:
  - 16165
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Server Programming
tags:
  - amazon
  - 'c#'
  - linq
  - rest
  - s3

---
One of the projects I have been working on is a system for managing content on our network of websites. One of our requirements is that changes don't take effect immediately, but on a separate preview network where our customer can look to see that her changes show the way she expected before pushing them into the production environment. Because of this requirement, we need to maintain 2 separate sets of product images (hosted on Amazon S3, with their CloudFront CDN used for the production sites).

Allowing our content management system to save image changes to Amazon and render their locations in our app was trivial, but we found the push mechanism for moving changes into the live environment a bit challenging. The work for moving database changes was already done when I started on the project, so most of the challenges I saw were in relation to copying the images. What we really wanted was a true bucket copy (emptying the destination bucket altogether and replacing its content with that of the source bucket) – but the [REST API for S3][1] does not support copy at the bucket level currently.

When designing our solution we needed to optimize not only on performance and maintainability, but on cost. The [pricing model for S3][2] has charges associated with most request types so it's not efficient to simply copy all the items in the source bucket to the destination. In addition, this wouldn't handle deleting objects that have been removed in the source bucket. So the naive solution becomes a two step process of deleting all of a bucket's content, then copying over all the content from the other bucket. In a scenario where the actual degree of change is likely to be minimal incurring this kind of cost is not a great option (and it's likely to perform poorly as well). When you add the challenge of CDN cache invalidation and [pricing for CloudFront][3] to the equation this looks even less attractive.

This post will go over the implementation we came up with for bucket copying. Code is written in C# using the [AWS SDK for .net][4], but it is all possible using the REST API directly. I will add that the AWS SDK is a very nice tool – typically I would choose to use [RestSharp][5] to go directly at the REST API, but Amazon has taken a lot of time to build a nice fluent syntax for building requests and some helper functions that make life easy enough to make it worth a look – it is far nicer than some of the other client libraries out there, in large part because it doesn't try to hide the fact that you're working with a set of webservices. But I digress. The process for copying a bucket is pretty straightforward:

  1. Check for destination bucket existence, create if needed
  2. List Objects in Both Buckets
  3. Identify keys for Insert, Update and Deletion (COPYs and DELETEs)
  4. Perform COPY / DELETE
  5. Invalidations (Updates only)

So here we go.</p> 

### Check for Bucket Existence

Checking for bucket existence is a piece of cake, thanks to a static helper function in the .net SDK that takes a bucket name and an s3 client. I chose to hide this call behind an instance method so that I could mock it during unit tests, but this is not strictly necessary. I think that behind the scenes this method simply issues a HEAD request on a bucket and returns false if a 404 is encountered. As you'd expect creating the bucket is done via a PUT request on the bucket resource. Code is as follows:

```csharp
void CreateDestinationBucketIfNeeded(string bucketName)
{
    if(!BucketExists(bucketName))
    {
        var request = new PutBucketRequest()
            .WithBucketName(bucketName);

        _s3Client.PutBucket(request);
    }
}

public virtual bool BucketExists(string bucketName)
{
    return Amazon.S3.Util.AmazonS3Util.DoesS3BucketExist(bucketName, _s3Client);
}
```

### List Objects in a Bucket

Listing objects in an S3 bucket is very easy. You just need to issue a signed GET request to myBucket.s3.amazonaws.com. The only gotcha is it only returns up to 1000 objects in a single response, so getting a complete list can take multiple requests. It helps to know a few things when putting this together – first that objects are listed in alphabetical order, second that we can include a “marker” parameter in our request telling AWS what key to start with, and third that the response from this method includes an “IsTruncated” flag. The C# code to list objects looks like this:

```csharp
IEnumerable<S3Object> ObjectsFor(string bucketName)
{
    var result = new List<S3Object>();

    var response = new ListObjectsResponse();
    do
    {
        var request = new ListObjectsRequest()
            .WithBucketName(bucketName);

        if(response.IsTruncated)
        {
            request.Marker = response.NextMarker;
        }

        response = _s3Client.ListObjects(request);
        result.AddRange(response.S3Objects);

    } while(response.IsTruncated);
    
    response.Dispose();

    return result;
}
```

### Identify Inserts, Updates and Deletes

Once we have the contents of both buckets, we need to determine which objects need to be inserted to, updated in, and deleted from the destination bucket. For deleted objects we will be issuing DELETE requests, and for Inserts and Updates we will be copying objects across using a special PUT request. The DELETE requests can be batched, supporting up to 1000 deletions in a single request, while the PUT requests need to be issued on an object by object basis.

These subsets of can be identified by comparing the objects in source and destination bucket. Deletes will include all objects in the destination that aren't in the source. Updates will include all objects present in both buckets that have changed (this can be determined by comparing the ETags on the objects). Inserts will of course contain all objects that exist in the source but not the destination.

C# helps us out a bit here, as LINQ makes it easy to do these comparisons without having to do any nasty looping. For the inserts and deletes we need to spoof an outer join which LINQ doesn't really have a great API for, but I still find it helpful to be able to think about these operations in set-based terms instead of in terms of the tedious iterative comparison that actually gets done behind all the LINQ magic.

Code to identify these sets of objects can be found here:

```csharp
IEnumerable<S3Object> ObjectsToUpdate(IEnumerable<S3Object> sourceObjects, IEnumerable<S3Object> destinationObjects)
{
    return from src in sourceObjects
           join dest in destinationObjects
               on src.Key equals dest.Key
           where src.Size > 0 && src.ETag != dest.ETag
           select src;
}

IEnumerable<S3Object> ObjectsToInsert(IEnumerable<S3Object> sourceObjects, IEnumerable<S3Object> destinationObjects)
{
    return from src in sourceObjects
           join dest in destinationObjects
               on src.Key equals dest.Key into joinedObjects
           from coalescedDest in joinedObjects.DefaultIfEmpty()
           where src.Size > 0 && coalescedDest == null
           select src;
}

IEnumerable<S3Object> ObjectsToDelete(IEnumerable<S3Object> sourceObjects, IEnumerable<S3Object> destinationObjects)
{
    return from dest in destinationObjects
           join src in sourceObjects
               on dest.Key equals src.Key into joinedObjects
           from coalescedSrc in joinedObjects.DefaultIfEmpty()
           where dest.Size > 0 && coalescedSrc == null
           select dest;
}
```

### Perform Insert/Delete

Inserts and deletes are the easy part. We just need to process the list of objects and issue the appropriate requests. There are two important optimizations here – we can batch deletes, and we can parallelize copies. Batching deletes will help to minimize network chatter, and parallelizing copies will help to force as much network chatter as possible into a shorter amount of time 🙂 I was torn on whether or not to paralellize the copies, but decided that in essence we are performing a ton of blocking I/O requests _**with another computer doing all the work**_. If you have real concerns about flooding your network connection you may not want to make this optimization, or do it in a way that allows you to dial down the amount of concurrency but after testing with the calls made in parallel I found a copy of a bucket with ~500 objects finished in about 25% of the time on my dual core laptop, and I was sold.

The inserts are the easiest part:

```csharp
void CopyObjects(IEnumerable<S3Object> items, Func<S3Object, CopyObjectRequest> requestBuilder)
{
    var exceptions = new ConcurrentQueue<Exception>();
    Parallel.ForEach(items, obj =>
    {
        try
        {
            var captured = obj;
            var request = requestBuilder(obj);
            _s3Client.CopyObject(request);
        }
        catch(Exception ex)
        {
            exceptions.Enqueue(ex);
        }
    });

    if(exceptions.Count > 0) throw new AggregateException(exceptions);
}
```

Deletes were pretty simple as well, only difference being the batching of requests:

```csharp
var toDelete = ObjectsToDelete(sourceObjects, destinationObjects).ToList();

while(toDelete.Count > 0)
{
    var batch = toDelete.Take(1000);
    var request = new DeleteObjectsRequest()
                        .WithBucketName(destinationBucket)
                        .WithKeys(batch.Select(k => new KeyVersion(k.Key)).ToArray());

    _s3Client.DeleteObjects(request);

    toDelete.RemoveRange(0, toDelete.Count > 1000 ? 1000 : toDelete.Count);
}
```

### Perform Updates

This step is not really anything special but it is different enough for me to exclude it from it's counterparts in step 3. We are basically doing the same copy operation for each object that we did for the inserts, but we also need to worry about cache invalidation on the CloudFront CDN. The invalidation can be batched, so it makes sense to build the list of keys updated while we do the copy, then send a single invalidation request.

So we can change the code for copy to something like this, taking an optional parameter containing a function to add the object's key to a list of keys to be invalidated:

```csharp
void CopyObjects(IEnumerable<S3Object> items, Func<S3Object, CopyObjectRequest> requestBuilder, Action<string> addToInvalidationList = null)
{
    var exceptions = new ConcurrentQueue<Exception>();
    Parallel.ForEach(items, obj =>
    {
        try
        {
            var captured = obj;
            var request = requestBuilder(obj);
            _s3Client.CopyObject(request);
            if(addToInvalidationList != null)
                addToInvalidationList(captured.Key);
        }
        catch(Exception ex)
        {
            exceptions.Enqueue(ex);
        }
    });

    if(exceptions.Count > 0) throw new AggregateException(exceptions);
}
```

I'll concede that this is not the simplest possible thing – it would probably be easier to call the old copy code and then pass the keys from the updated objects collection, but I do like that it captures the keys of the objects that are actually copied. If we changed the approach to do something like what is happening with the delete operation that trims the collection while processing, the keys collection we pass for invalidation would end up empty. I could also just have too much functional programming on the brain I guess 😉

Once we have the list of keys, performing the actual invalidation is relatively simple. We do need to generate a unique “caller reference” that amazon uses to ensure that duplicate requests aren't processed (remember, each object invalidated triggers some kind of action on potentially hundreds of servers, so this is not a cheap operation for you OR for Amazon). The hardest part is probably identifying whether there is a CloudFront distribution to worry about in the first place. The key to finding whether there is a distribution attached to a bucket or not relies on the knowledge that for an s3 origin, the DNSName property will be “bucketname.s3.amazonaws.com” – so by stripping out “.s3.amazonaws.com” from our available distributions' DNS names we can find if one matches our bucket.

The code for invalidation looks like this:

```csharp
const string dateFormatWithMilliseconds = "yyyy-MM-dd hh:mm:ss.ff";

void InvalidateObjects(string destinationBucket, List<string> keysToInvalidate)
{
    if(keysToInvalidate.Count > 0)
    {
        var distId = GetDistributionIdFor(destinationBucket);
        if(!string.IsNullOrWhiteSpace(distId))
        {
            var invalidationRequest = new PostInvalidationRequest()
                .WithDistribtionId(distId)
                .WithInvalidationBatch(new InvalidationBatch(DateTime.Now.ToString(dateFormatWithMilliseconds),
                                                                     keysToInvalidate));

            _cloudFrontClient.PostInvalidation(invalidationRequest);
        }
    }
}

const string amazonBucketUriSuffix = ".s3.amazonaws.com";

string GetDistributionIdFor(string bucketName)
{
    var distributionNameAndIds =
        _cloudFrontClient.ListDistributions()
        .Distribution
        .ToDictionary(cfd => cfd.DistributionConfig.S3Origin.DNSName.Replace(amazonBucketUriSuffix, ""), cfd => cfd.Id);

    string id = null;
    distributionNameAndIds.TryGetValue(bucketName, out id);
    return id;
}
```

### Tying it All Together

OK so we have all these methods to facilitate copying buckets but how do we actually do it? I'm glad you asked.

```csharp
public void Copy(string sourceBucket, string destinationBucket)
{
    CreateDestinationBucketIfNeeded(destinationBucket);

    var sourceObjects = ObjectsFor(sourceBucket);
    var destinationObjects = ObjectsFor(destinationBucket);

    var toDelete = ObjectsToDelete(sourceObjects, destinationObjects).ToList();
    while(toDelete.Count > 0)
    {
        var batch = toDelete.Take(1000);
        var request = new DeleteObjectsRequest()
            .WithBucketName(destinationBucket)
            .WithKeys(batch.Select(k => new KeyVersion(k.Key)).ToArray());

        _s3Client.DeleteObjects(request);

        toDelete.RemoveRange(0, toDelete.Count > 1000 ? 1000 : toDelete.Count);
    }

    var buildCopyRequest = new Func<S3Object, CopyObjectRequest>(s3obj => new CopyObjectRequest()
        .WithSourceBucket(sourceBucket)
        .WithDestinationBucket(destinationBucket)
        .WithSourceKey(s3obj.Key)
        .WithDestinationKey(s3obj.Key)
        .WithCannedACL(S3CannedACL.PublicRead));

    CopyObjects(ObjectsToInsert(sourceObjects, destinationObjects), buildCopyRequest);

    var keysToInvalidate = new ConcurrentBag<string>();

    CopyObjects(ObjectsToUpdate(sourceObjects, destinationObjects), buildCopyRequest, keysToInvalidate.Add);

    InvalidateObjects(destinationBucket, keysToInvalidate.ToList());
}
```

Note that the delete happens inline instead of in a separate method as with the copies. This is because it wasn't reusable, and because the act of deleting the items changes the collection. As a result it seemed cleaner to just do it inline.

This is really all there is to it. Hopefully copying buckets in this manner is something that finds its way into the REST API for S3 at some point, but in the meantime this process seems to be working pretty well. It is not the prettiest code, but it runs reasonably fast for our smallish buckets, and I think it is doing a good job minimizing what our customer has to pay for the service each month. It was interesting working on this problem, because it forced thinking about the problem in terms of Amazon's pricing structure (though because Amazon doesn't charge for data transfer within s3 regions it really becomes the same as thinking in terms of minimizing http requests). This kind of thing isn't always a driver of implementation so it was a nice mental exercise. Now we just have to hope they never change their pricing structure 😉

 [1]: http://docs.amazonwebservices.com/AmazonS3/latest/API/RESTBucketOps.html
 [2]: http://aws.amazon.com/s3/pricing/
 [3]: http://aws.amazon.com/cloudfront/pricing/
 [4]: http://aws.amazon.com/sdkfornet/
 [5]: https://github.com/restsharp/restsharp