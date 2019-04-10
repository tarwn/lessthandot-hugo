---
title: SquishIt and Nancy – Part Deux
author: Alex Ullrich
type: post
date: 2014-02-21T02:53:31+00:00
ID: 2430
url: /index.php/webdev/serverprogramming/squishit-and-nancy-part-deux/
views:
  - 6814
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Server Programming
tags:
  - nancy
  - squishit

---
About a year ago, I wrote about getting SquishIt up and running with the (awesome) [Nancy Web Framework][1].  You can read all about that [here][2].  Since then, things have changed a bit, as they are wont to do.  I had mostly been ignoring this project, until an issue came up on the SquishIt mailing list that forced me to fire it up again. The biggest change I found is that Nancy no longer depends on System.Web.  This really messed up the file path resolution in SquishIt, and forced us to introduce a new extensibility point for path translation.  Frankly, this probably should have been done a long time ago, but at least it is getting done now.

It was a pretty simple change but I'll go over it here.  First we needed to introduce a new interface, IPathTranslator.

```csharp
namespace SquishIt.Framework
{
    public interface IPathTranslator
    {
        string ResolveAppRelativePathToFileSystem(string file);
        string ResolveFileSystemPathToAppRelative(string file);
    }
}
```

This takes the place of the old “FileSystem” static class, that probably only a few people are aware of. The implementation isn't that important, but it takes some information from HttpRuntime / HttpContext and uses that to convert between file locations on disk and app-relative web paths. 

Because newer versions of Nancy don't reference System.Web, we had to find another way to do this. It seems like the best way to get it done is using Nancy's IRootPathProvider. The following looks like a reasonable approximation of what was being done with HttpContext / HttpRuntime – I have not tested it with CSS path rewriting or any of the trickier stuff that SquishIt does, but I think it will work. If not I'm sure someone will let me know.

```csharp
using System;
using Nancy;
using SquishIt.Framework;

namespace SquishIt.NancySample
{
    public class NancyPathTranslator : IPathTranslator
    {
        private readonly IRootPathProvider _rootPathProvider;

        public NancyPathTranslator(IRootPathProvider rootPathProvider)
        {
            _rootPathProvider = rootPathProvider;
        }

        public string ResolveAppRelativePathToFileSystem(string file)
        {
            // Remove query string
            if(file.IndexOf('?') != -1)
            {
                file = file.Substring(0, file.IndexOf('?'));
            }

            return _rootPathProvider.GetRootPath() + "/" + file.TrimStart('~').TrimStart('/');
        }

        public string ResolveFileSystemPathToAppRelative(string file)
        {
            var root = new Uri(_rootPathProvider.GetRootPath());
            return root.MakeRelativeUri(new Uri(file, UriKind.RelativeOrAbsolute)).ToString();
        }
    }
}
```

Pretty simple. We basically had to replace the Server.MapPath / HttpRuntime.AppDomainAppPath type stuff we were using with IRootPathProvider.GetRootPath(). It may very well end up being more complicated than this – but whats important is it is now in the user's control. In addition to allowing users of newer versions of Nancy to use SquishIt, this will also let users with environmental issues that our code does not account for work around our bad code without needing to dig too deeply into SquishIt's internals.

Once this is coded, we just have to configure SquishIt to use it. This can be done in a nancy bootstrapper like so:

```csharp
Bundle.ConfigureDefaults()
    .UsePathTranslator(new NancyPathTranslator(new AspNetRootSourceProvider()));
```

Once this is done, we can run the project successfully again, and go back to not worrying about this until another Nancy user points out the next problem. For a closer look, the sample project is available at [github][3]. Big thanks to SquishIt/Nancy user Mike Ward for pointing out on the mailing list that things weren't working anymore. It would be impossible to stay on top of this kind of stuff without the help of people like him.

 [1]: http://nancyfx.org/
 [2]: /index.php/webdev/serverprogramming/aspnet/squishit-and-nancy/
 [3]: https://github.com/AlexCuse/SquishIt.NancySample