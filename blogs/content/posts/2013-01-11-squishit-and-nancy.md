---
title: SquishIt and Nancy
author: Alex Ullrich
type: post
date: 2013-01-11T13:11:00+00:00
ID: 1909
excerpt: "Everybody's favorite LTD blogger / Belgian tweeter Chris asked me last week how he could get SquishIt working with the Nancy web framework.  I had to admit, I had no idea.  But couldn't imagine it would be that much more difficult than making it work wi&hellip;"
url: /index.php/webdev/serverprogramming/aspnet/squishit-and-nancy/
views:
  - 18398
rp4wp_auto_linked:
  - 1
categories:
  - AJAX
  - ASP.NET
tags:
  - nancy
  - squishit

---
Everybody's favorite [LTD blogger][1] / [Belgian tweeter][2] Chris asked me last week how he could get SquishIt working with the [Nancy web framework][3]. I had to admit, I had no idea. But couldn't imagine it would be that much more difficult than making it work with ASP.net MVC. So I decided to look into it. It turned out to be pretty much the same, with one extra step. I started out by installing the packages I needed from NuGet to an empty asp.net application:

```text
> install-package SquishIt
> install-package Nancy
> install-package Nancy.Hosting.AspNet
> install-package Nancy.Viewengines.Razor
```

Once installed, there is a little bit of setup work we need to do.

### Configuring Nancy's View Engine

This was infinitely more complex than using a referenced library in a razor view with MVC. Translation: this was as simple as adding a “razor” section to the web.config:

```xml
<configSections>
    <section name="razor" type="Nancy.ViewEngines.Razor.RazorConfigurationSection, Nancy.ViewEngines.Razor" />
  </configSections>
  <razor disableAutoIncludeModelNamespace="false">
    <assemblies>
      <add assembly="System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
      <add assembly="SquishIt.Framework" />
    </assemblies>
    <namespaces>
      <add namespace="SquishIt.Framework" />
    </namespaces>
  </razor>
```

Its worth noting that when I added SquishIt.Framework to the namespaces section it **worked**, but didn't help with intellisense. So I ended up adding the @using directives in my views anyway. So if you want intellisense, don't bother with the namespaces if redundancy bothers you.

### Static Bundles

The typical SquishIt use involves writing a new css or javascript file to the server's file system. At least I think it does &#8211; this is certainly what I would consider typical. So I looked at that first. First thing I needed to do was figure out how to get Nancy to render a view for me. It wasn't terribly difficult, just had to set up a module with the routes involved:

```csharp
using Nancy;

namespace SquishIt.NancySample.Modules
{
    public class HomeModule : NancyModule
    {
        public HomeModule()
        {
            Get["/"] = parameters => View["Hello.cshtml"];
        }
    }
}
```

By convention, Nancy locates the view in Views/Home. I assume it would look in Views/Shared next, but didn't bother to confirm. So I added couple javascript files in Content/js, and then added a view with a bundle:

```razor
@using SquishIt.Framework
<!DOCTYPE html>

<html lang="en">
    <head>
        <meta charset="utf-8" />
        <title>Hello World Page</title>
    </head>
    <body>
        <h1>Hello World Page</h1>
        <p>Hello World!</p>
        <p>This page will include a javascript bundle that is rendered to the file system and served as a static asset.</p>
        @Html.Raw(Bundle.JavaScript()
            .Add("~/Content/js/js1.js")
            .Add("~/Content/js/js2.js")
            .Render("~/Content/combined/bundle.js"))
    </body>
</html>
```

As long as I disabled debugging, a single tag was rendered into my page for bundle.js. That was easy.

### Cached Bundles

SquishIt also has the ability to render bundles to an internal cache instead of the file system. This is useful for shared hosting environments. You can read the initial documentation [here][4]. Getting this to work with Nancy was not really that different &#8211; we just needed to create a module to handle serving the assets:

```csharp
using System.IO;
using System.Text;
using Nancy;
using SquishIt.Framework;

namespace SquishIt.NancySample.Modules
{
    public class AssetsModule : NancyModule
    {
        public AssetsModule()
            : base("/assets")
        {
            Get["/js/{name}"] = parameters => CreateResponse(Bundle.JavaScript().RenderCached((string)parameters.name), Configuration.Instance.JavascriptMimeType);
            Get["/css/{name}"] = parameters => CreateResponse(Bundle.Css().RenderCached((string)parameters.name), Configuration.Instance.CssMimeType);
        }

        Response CreateResponse(string content, string contentType)
        {
            return Response
                .FromStream(() => new MemoryStream(Encoding.UTF8.GetBytes(content)), contentType)
                .WithHeader("Cache-Control", "max-age=45");
        }
    }
}
```

This module renders a cached bundle by name using SquishIt's globally configured MIME types to render the content. It also sets a cache-control header on the response, just because I wanted to see how to set headers with Nancy.

We then need to add a Global.asax and build a bundle:

```csharp
protected void Application_Start(object sender, EventArgs e)
{
    Bundle.JavaScript()
        .Add("~/Content/js/js1.js")
        .Add("~/Content/js/js2.js")
        .AsCached("hello", "~/assets/js/hello");
}
```

The second parameter here is called filePath, but actually represents the path to the assets controller, including the “name” parameter. This is what gets used in the src attribute of the rendered tag.

Finally we can add a view. Note that the cached bundle is rendered by name into the page:

```razor
@using SquishIt.Framework
<!DOCTYPE html>

<html lang="en">
    <head>
        <meta charset="utf-8" />
        <title>Hello World Page</title>
    </head>
    <body>
        <h1>Hello World Page</h1>
        <p>Hello World!</p>
        <p>This page will include a javascript bundle that is rendered into memory in Global.asax and served through the Assets Module</p>
        @Html.Raw(Bundle.JavaScript()
            .RenderCachedAssetTag("hello"))
    </body>
</html>
```

and change our HomeModule to serve the route:

```csharp
using Nancy;

namespace SquishIt.NancySample.Modules
{
    public class HomeModule : NancyModule
    {
        public HomeModule()
        {
            Get["/"] = parameters => View["Hello.cshtml"];
            Get["/cached"] = parameters => View["HelloCached.cshtml"];
        }
    }
}
```

Piece of cake.

### Conclusion

This was my first exposure to Nancy, and I came away pretty impressed. Its no-nonsense approach reminds me of other projects I've messed around with in the past like [manos][5] and [ServiceStack][6]. I hope to get a chance to play around with it at least a little bit more.

I'll think about putting together a package to help with integration (similar to [SquishIt.Mvc][7] but its so easy to get going that I'm not sure its needed (that package is only a controller and a few extension methods that return MvcHtmlStrings instead of strings). I guess I will have to see if there is any demand, or if there are any issues preventing Nancy from being used in shared hosting environments to see if it'd be worth it.

The sample project can be downloaded in its' entirety at [github][8].

 [1]: /index.php/All/?disp=authdir&author=7
 [2]: http://twitter.com/chrissie1
 [3]: http://nancyfx.org/
 [4]: https://github.com/jetheredge/SquishIt/wiki/Using-SquishIt-programmatically-without-the-file-system
 [5]: http://manosdemono.org/
 [6]: http://servicestack.net/
 [7]: http://nuget.org/packages/SquishIt.Mvc/
 [8]: https://github.com/AlexCuse/SquishIt.NancySample/tree/blog-20130111