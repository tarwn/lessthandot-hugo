---
title: SquishIt Integration with Amazon S3 / Cloudfront
author: Alex Ullrich
type: post
date: 2012-06-12T11:00:00+00:00
ID: 1646
excerpt: 'For the unfortunate souls not in the know (or is it the fortunate souls using one of the myriad alternatives?), SquishIt is a library used to optimize content delivery at runtime in ASP.net applications.  It combines and minifies javascript files, and a&hellip;'
url: /index.php/webdev/serverprogramming/making-squishit-work-with-amazon/
views:
  - 22787
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Server Programming
tags:
  - squishit

---
For the unfortunate souls not in the know (or is it the fortunate souls using one of the myriad alternatives?), [SquishIt][1] is a library used to optimize content delivery at runtime in ASP.net applications. It combines and minifies javascript files, and also does a bit of preprocessing for things like [LESS][2] and [CoffeeScript][3]. I've been working with Justin Etheredge ([blog][4]|[twitter][5]) on this for a while, first on patches for various bugs I encountered trying to use SquishIt on linux, but more recently my focus has been on improving extensibility. One of the areas that I really felt the library could benefit from increased extensibility is the area of CDN support. I've been doing a lot of work with Amazon CloudFront lately, and decided it would be cool to see how cleanly I could get SquishIt to work with the service.

### SquishIt CDN Support in Previous Versions

I suppose it makes sense to start with what we already had in place. CDN support in SquishIt has been slowly progressing, largely thanks to community contributions. As of version 0.8.6 we did have support for injecting a base URL into asset paths, but this required you to know the generated file name in advance and upload it to your CDN through other means. While this worked, and could be easily automated in your build process, it wasn't exactly convenient. The pull requests we've gotten have done a fairly good job showing us what the community wants in terms of CDN support, so it feels like it is time to start trying to treat it as a first class citizen. The first thing I would like is a way to render the combined file directly to my CDN if it doesn't already exist, and maybe a way to force overwriting the file if need be.

### Adding Support for Custom Renderers

For a while now, SquishIt has had an IRenderer interface. It has been there, but it has only been used internally to support rendering to the file system or to an in-memory cache. To get started, we needed to expose this interface publicly so that other assemblies could provide implementations. Once exposed, we need to enable consumers to supply their custom renderers to the SquishIt core somehow.

SquishIt uses a fluent configuration syntax for setting up individual bundles, and that seemed as good a place to start as any. Typical usage looks something like this:

```csharp
Bundle.JavaScript()
    .WithAttribute("attrName", "attrValue")
    .Add("file1.js")
    .Add("/otherscripts/file2.js")
    .Render("combinedOutput.js");
```

So the first thing that came to mind was to add something like a "WithFileRenderer" method. This would give us a way to inject a renderer into a bundle and have it used to render the combined files. However, we probably don't want to render to the CDN while debugging, so "WithReleaseFileRenderer" might be more appropriate. Setting up the method went something like this:

```csharp
IRenderer releaseFileRenderer;

public T WithReleaseFileRenderer(IRenderer renderer)
{
    this.releaseFileRenderer = renderer;
    return (T)this;
}
```
We also want a way to configure this globally, to do that we needed to add a bit to our configuration class. This was basically the same thing:

```csharp
IRenderer _defaultReleaseRenderer;
public Configuration UseReleaseRenderer(IRenderer releaseRenderer)
{
    _defaultReleaseRenderer = releaseRenderer;
    return this;
}
```

Finally, we need to change the way the file renderer is obtained when we go to render the combined assets. Previously we were instantiating a new FileRenderer or CacheRenderer depending on circumstance, and passing that renderer into the main rendering method. This won't cut it anymore, as our needs have gotten significantly more complex. The constraints we have to deal with are as follows:

  * When debugging we should use a normal file renderer
  * We should favor a renderer configured at the instance level over one configured statically
  * If no instance or static renderer is configured we should use the old default behavior

So the constructor calls for a new FileRenderer are replaced with calls to this method:

```csharp
protected IRenderer GetFileRenderer()
{
    return debugStatusReader.IsDebuggingEnabled() ? new FileRenderer(fileWriterFactory) :
        bundleState.ReleaseFileRenderer ??
        Configuration.Instance.DefaultReleaseRenderer() ??
        new FileRenderer(fileWriterFactory);
}
```

This is basically all we needed to do to enable us to plug in a custom renderer to use in release mode. Now we can look at how we can make the CDN integration happen.

### Building the S3 Keys

The only really tricky thing about building the renderer is that it takes a string representing the disk location to render to. Changing what the renderer takes as a parameter would involve a more significant change to the core behavior than I'm comfortable making right now, so the first thing we need is a way to turn these disk locations into keys that S3 can use. The key we create needs to match the relative path to that of the locally-rendered asset so that injecting the base url will yield the absolute path that we need.

None of this is terribly difficult – the main edge cases we need to cover are

  * Root appearing twice in the file path (because of Windows' drive lettering this is mostly an issue running on unix-based systems)
  * Virtual directories

To meet these requirements the two pieces of information that we need inside the key builder are the physical application path and virtual directory. Here are some tests:

```csharp
[Test]
public void ReturnToRelative()
{
    var root = @"C:fakedir";
    var file = @"anotherfile.js";
    var expected = @"another/file.js";

    var builder = new KeyBuilder(root, "");
    Assert.AreEqual(expected, builder.GetKeyFor(root + file));
}

[Test]
public void ReturnToRelative_Injects_Virtual_Directory()
{
    var root = @"C:fakedir";
    var file = @"anotherfile.js";
    var vdir = "/this";
    var expected = @"this/another/file.js";

    var builder = new KeyBuilder(root, vdir);
    Assert.AreEqual(expected, builder.GetKeyFor(root + file));
}

[Test]
public void ReturnToRelative_Only_Replaces_First_Occurrence_Of_Root()
{
    var root = @"test/";
    var file = @"another/andthen/test/again.js";
    var expected = @"another/andthen/test/again.js";

    var builder = new KeyBuilder(root, "");
    Assert.AreEqual(expected, builder.GetKeyFor(root + file));
}
```

And the implementation for the KeyBuilder:

```csharp
internal class KeyBuilder : IKeyBuilder
{
    readonly string physicalApplicationPath;
    readonly string virtualDirectory;

    public KeyBuilder(string physicalApplicationPath, string virtualDirectory)
    {
        this.physicalApplicationPath = physicalApplicationPath;
        this.virtualDirectory = virtualDirectory;
    }

    public string GetKeyFor(string path)
    {
        return RelativeFromAbsolutePath(path).TrimStart('/');
    }

    string RelativeFromAbsolutePath(string path)
    {
        path = path.StartsWith(physicalApplicationPath)
                        ? path.Substring(physicalApplicationPath.Length)
                        : path;

        return virtualDirectory + "/" + path.Replace(@"", "/").TrimStart('/');
    }
}
```

Now that we have a means to build keys we can look at implementing the S3 Renderer.

### S3 Renderer Implementation

The interface for renderers is very simple.

```csharp
public interface IRenderer
{
    void Render(string content, string outputPath);
}
```

The only things we'll need to implement this method are an initialized S3 client, a bucket and the key builder we implemented in the last section. By default, we won't want to upload our content if it already exists on the CDN, so we will need to check for existence before uploading the content. This can be done by querying for object metadata using the desired key – if the file doesn't exist we will get a "not found" status on the exception thrown by the s3 client. So the most important test will look like this:

```csharp
[Test]
public void Render_Uploads_If_File_Doesnt_Exist()
{
    var s3client = new Mock<AmazonS3>();
    var keyBuilder = new Mock<IKeyBuilder>();

    var key = "key";
    var bucket = "bucket";
    var path = "path";
    var content = "content";

    keyBuilder.Setup(kb => kb.GetKeyFor(path)).Returns(key);

    s3client.Setup(c => c.GetObjectMetadata(It.Is<GetObjectMetadataRequest>(gomr => gomr.BucketName == bucket && gomr.Key == key))).
        Throws(new AmazonS3Exception("", HttpStatusCode.NotFound));

    using(var renderer = S3Renderer.Create(s3client.Object)
                            .WithBucketName(bucket)
                            .WithKeyBuilder(keyBuilder.Object))
    {
        renderer.Render(content, path);
    }

    s3client.Verify(c => c.PutObject(It.Is<PutObjectRequest>(por => por.Key == key &&
                                                                        por.BucketName == bucket &&
                                                                        por.ContentBody == content &&
                                                                        por.CannedACL == S3CannedACL.NoACL)));
}
```

Note that it is checking the PutObjectRequest to ensure that the ACL used is "NoACL". This is probably not an optimal default (most people will want the "PublicRead" ACL I imagine) but I decided to err on the side of caution and force people to opt-in to making their content publicly visible. The implementation for the render method looks something like this:

```csharp
public void Render(string content, string outputPath)
{
    if(string.IsNullOrEmpty(outputPath) || string.IsNullOrEmpty(content)) throw new InvalidOperationException("Can't render to S3 with missing key/content.");

    var key = keyBuilder.GetKeyFor(outputPath);
    if(!FileExists(key))
    {
        UploadContent(key, content);
    }
}

void UploadContent(string key, string content)
{
    var request = new PutObjectRequest()
        .WithBucketName(bucket)
        .WithKey(key)
        .WithCannedACL(cannedACL)
        .WithContentBody(content);

    s3client.PutObject(request);
}

bool FileExists(string key)
{
    try
    {
        var request = new GetObjectMetadataRequest()
            .WithBucketName(bucket)
            .WithKey(key);

        var response = s3client.GetObjectMetadata(request);

        return true;
    }
    catch(AmazonS3Exception ex)
    {
        if(ex.StatusCode == HttpStatusCode.NotFound)
        {
            return false;
        }
        throw;
    }
}
```

It has gotten slightly more complex since then (I've added configurable headers and an option for forcing overwrite of the existing file) but the core logic remains the same. It's a fairly naive implementation but my experience with the Amazon services has been good enough so far that I haven't encountered a lot of the exceptions that I hope to add handling for in the future.

### Adding Invalidation

At this point we should be able to render our content directly to S3, but this doesn't get us all the way to where we need to be. While hosting static content in S3 offers some advantages over hosting it locally and it **can** work as a CDN, using CloudFront to deliver your S3 content makes more sense if you really want to minimize latency. To make this work we'll just need to add an invaliator to the mix. A test for the core usage looks like this:

```csharp
[Test]
public void Invalidate()
{
    var cloudfrontClient = new Mock<AmazonCloudFront>();

    var distributionId = Guid.NewGuid().ToString();
    var bucket = Guid.NewGuid().ToString();
    var distribution = bucket + ".s3.amazonaws.com";
    var key = Guid.NewGuid().ToString();

    var listDistributionsResponse = new ListDistributionsResponse();
    listDistributionsResponse.Distribution.Add(new CloudFrontDistribution
    {
        Id = distributionId,
        DistributionConfig = new CloudFrontDistributionConfig
        {
            S3Origin = new S3Origin(distribution, null)
        }
    });

    cloudfrontClient.Setup(cfc => cfc.ListDistributions())
        .Returns(listDistributionsResponse);

    var invalidator = new CloudFrontInvalidator(cloudfrontClient.Object);
    invalidator.InvalidateObject(bucket, key);

    cloudfrontClient.Verify(cfc => cfc.PostInvalidation(It.Is<PostInvalidationRequest>(pir => pir.DistributionId == distributionId
        && pir.InvalidationBatch.Paths.Count == 1
        && pir.InvalidationBatch.Paths.First() == key)));
}
```

The implementation is pretty straightforward, and will look very familiar to anyone who read my post regarding [copying buckets with the S3 API][6]. There are only two changes, first that we only need to invalidate one object at a time, and second that we only want to query for the list of CloudFront distributions once. Code for the CloudFront invalidator looks like this:

```csharp
class CloudFrontInvalidator : IDisposable, IInvalidator
{
    const string amazonBucketUriSuffix = ".s3.amazonaws.com";
    const string dateFormatWithMilliseconds = "yyyy-MM-dd hh:mm:ss.ff";
    readonly AmazonCloudFront cloudFrontClient;

    public CloudFrontInvalidator(AmazonCloudFront cloudFrontClient)
    {
        this.cloudFrontClient = cloudFrontClient;
    }

    public void InvalidateObject(string bucket, string key)
    {
        var distId = GetDistributionIdFor(bucket);
        if(!string.IsNullOrWhiteSpace(distId))
        {
            var invalidationRequest = new PostInvalidationRequest()
                .WithDistribtionId(distId)
                .WithInvalidationBatch(new InvalidationBatch(DateTime.Now.ToString(dateFormatWithMilliseconds), new List<string> { key }));

            cloudFrontClient.PostInvalidation(invalidationRequest);
        }
    }

    Dictionary<string, string> distributionNameAndIds;

    string GetDistributionIdFor(string bucketName)
    {
        distributionNameAndIds = distributionNameAndIds ??
            cloudFrontClient.ListDistributions()
            .Distribution
            .ToDictionary(cfd =>
                cfd.DistributionConfig.S3Origin.DNSName.Replace(amazonBucketUriSuffix, ""),
                cfd => cfd.Id);

        string id = null;
        distributionNameAndIds.TryGetValue(bucketName, out id);
        return id;
    }

    public void Dispose()
    {
        cloudFrontClient.Dispose();
    }
}
```

As an interesting aside, while I was working on this Amazon released [support for querystring invalidation/versioning][7], which is SquishIt's default behavior. I had planned to add a release note telling people that they would need to use squishit's "hash in filename" option, but it seems like now there won't be any need 🙂

### Neat, But How Do I Use This?

It's nice that this all works on paper (and in unit tests) but how do we actually tie everything together? One of the key design decisions was that the renderer is instantiated with pre-initialized CloudFront and S3 clients. This way users aren't locked into a certain method of getting credentials or anything like that. To use the custom renderer for only a particular bundle usage would be something like this:

```csharp
var s3client = new AmazonS3Client("accessKey", "secretKey");
var renderer = S3Renderer.Create(s3client)
    .WithBucketName("bucket")
    .WithDefaultKeyBuilder(HttpContext.Current.Request.PhysicalApplicationPath,
                                    HttpContext.Current.Request.ApplicationPath)
    .WithCannedAcl(S3CannedACL.PublicRead) as IRenderer;

Bundle.JavaScript()
    .WithReleaseFileRenderer(renderer)
    .WithOutputBaseHref("http://s3.amazonaws.com/bucket")
    .Add("file1.js")
    .Add("file2.js")
    .Render("combined.js");
```
This is nice, but I think the global configuration is probably what people will be using more often. As is common in ASP.net apps a lot of the setup magic for this happens in the app initialization. So you'd add something like this to your Application_Start method (in Global.asax.cs):

```csharp
var s3client = new AmazonS3Client("accessKey", "secretKey");
var renderer = S3Renderer.Create(s3client)
    .WithBucketName("bucket")
    .WithDefaultKeyBuilder(HttpContext.Current.Request.PhysicalApplicationPath,
                                    HttpContext.Current.Request.ApplicationPath)
    .WithCannedAcl(S3CannedACL.PublicRead) as IRenderer;

Bundle.ConfigureDefaults()
    .UseReleaseRenderer(renderer)
    .UseDefaultOutputBaseHref("http://s3.amazonaws.com/bucket");
```

I tried to make this something that could be run via WebActivator, but had trouble finding a method to use that would have access to the HttpContext (needed to resolve application path and virtual directory) so for now it needs to be set up manually. This may be for the best though, as it doesn't force any particular convention for access key / secret key retrieval. It doesn't **feel** like a ton of setup code to me, hopefully others will agree.

### What's Next

Now that SquishIt 0.8.7 has been released I can finally start planning to make this available [on NuGet][8] as a standard package (currently in beta until I get a little more testing). It can be installed like any other, but will require updating SquishIt if you're using a pre-0.8.7 version. If you need to report any issues encountered while using the library, or feel like contributing some code, please do so [on github][9]. Oh, and if you just want to kick the tires on SquishIt without all this other nonsense, or try making it work with another CDN you can find the core library [on NuGet][10] as well.

 [1]: https://github.com/jetheredge/SquishIt
 [2]: http://lesscss.org/
 [3]: http://coffeescript.org/
 [4]: http://www.codethinked.com/
 [5]: https://twitter.com/#!/justinetheredge
 [6]: /index.php/WebDev/ServerProgramming/copying-buckets-with-the-amazon-s3-api
 [7]: http://aws.amazon.com/releasenotes/7875688065681094
 [8]: http://nuget.org/packages/SquishIt.S3
 [9]: https://github.com/AlexCuse/SquishIt.S3
 [10]: http://nuget.org/packages/SquishIt