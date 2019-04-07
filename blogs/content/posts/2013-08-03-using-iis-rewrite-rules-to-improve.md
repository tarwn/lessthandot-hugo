---
title: Using IIS Rewrite Rules With SquishIt Cache Invalidation
author: Alex Ullrich
type: post
date: 2013-08-03T13:32:00+00:00
ID: 2104
excerpt: 'In version 0.9.2 and earlier, SquishIt had two options for handling browser cache invalidation.  The default behavior was to append the hash to the query string, and the other was to include the hash in the combined filename.  While both got the job don&hellip;'
url: /index.php/webdev/serverprogramming/using-iis-rewrite-rules-to-improve/
views:
  - 10080
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Microsoft IIS
  - Server Programming
tags:
  - asp.net
  - squishit

---
In version 0.9.2 and earlier, SquishIt had two options for handling browser cache invalidation. The default behavior was to append the hash to the query string, and the other was to include the hash in the combined filename. While both got the job done, they both came with advantages and disadvantages. This post will attempt to cover those while also introducing a third option that is available starting in version 0.9.3.

### Querystring Invalidation

SquishIt&#8217;s default versioning behavior is to append the versioning hash to the URL of a combined file as a query string parameter. So a bundle set up like this:

```csharp
Bundle.JavaScript()
   .Add("/assets/js/jquery_1.7.2.js")
   .Add("/assets/js/minifyjs_test.js")
   .ForceRelease()
   .Render("/output/minifyjs_test_output.js")
```
Would render a script tag like this:

```html
<script type="text/javascript" src="/output/minifyjs_test_output.js?{hashKeyName}={invalidationHash}"></script>
```

The main disadvantage of this is that it doesn&#8217;t work with all caching proxies, though it seems to be pretty consistently supported in modern browsers. The advantage is that it only requires one set of combined files to be stored on the server. This is usually the best choice for files served locally because it doesn&#8217;t require any cleanup of old files on the server.

### Filename Invalidation

When using this strategy, hashes are written directly into the filename. So a bundle set up like this:

```csharp
Bundle.JavaScript()
   .Add("/assets/js/jquery_1.7.2.js")
   .Add("/assets/js/minifyjs_test.js")
   .ForceRelease()
   .Render("/output/minifyjs_test_output#.js")
```
Would render a script tag like this:

```html
<script type="text/javascript" src="/output/minifyjs_test_output{invalidationHash}.js"></script>
```

The main disadvantage of this strategy is that it tends to accumulate files over time. Because the hash is generated off of the combined file&#8217;s contents, every time a bundled script file or stylesheet changes, a new combined file is created. It eventually becomes necessary to clean this stuff up (The easiest way is to delete all files and reset the app pool, otherwise its usually safe to delete all but the most recent version for each combined file). The main advantage is that it is supported by all caching proxies &#8211; this consistent behavior makes it a good choice for CDN environments where you typically need to manage multiple versions of files anyway.

### Folder Invalidation

This new strategy is similar to the filename invalidation strategy when it comes to output file naming, but behaves more like querystring invalidation in terms of disk footprint. It is used similarly to the hash in filename option, in that you simply put a hash symbol in the path where you want the content&#8217;s hash to show up. Unlike the hash in filename method, it requires you to use it explicitly because we need to be able to figure out the right folder to write files to. So a bundle set up like this:

```csharp
Bundle.JavaScript()
   .Add("/assets/js/jquery_1.7.2.js")
   .Add("/assets/js/minifyjs_test.js")
   .WithCacheInvalidationStrategy(new HashAsVirtualDirectoryCacheInvalidationStrategy())
   .ForceRelease()
   .Render("/output/#/minifyjs_test_output.js")
```
Would render a script tag like this:

```html
<script type="text/javascript" src="/output/{invalidationHash}/minifyjs_test_output.js"></script>
```

From looking at these URLs it is clear that caching agents will handle this the same way they handled the URLs built with the filename invalidation strategy. But the strategy actually scrubs the hash from the disk location for the bundle, meaning that only one file is generated. We can then set up a rewrite rule to scrub the hash out of the URL for incoming requests like so.

So for IIS we could do something like this in our web.config:

```xml
<system.webServer>
    <!-- snip -->
    <rewrite>
      <rules>
        <rule name="squishit">
          <match url="([S]+)(/r-[w]+/)([S]+)"  />
          <action type="Rewrite" url="{R:1}/{R:3}" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
```

To configure these options globally we can add the following in Application_Start:

```csharp
Bundle.ConfigureDefaults().UseCacheInvalidationStrategy(new HashAsVirtualDirectoryCacheInvalidationStrategy()); 
```

This will result in the supplied strategy being used for all bundles **unless** you override on a bundle using the method shown above.

The end result combines the advantages of querystring invalidation and filename invalidation for what should be a minimal performance hit. Hopefully this comes in handy in the future.