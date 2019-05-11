---
title: SPA Routing in ASP.Net Core
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-06-30T11:58:40+00:00
ID: 8664
url: /index.php/webdev/serverprogramming/aspnet/spa-routing-in-asp-net-core/
views:
  - 3731
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - 'C#'
tags:
  - asp.net core
  - spa

---
One of the challenges of SPA applications is making sure a bookmark or hard refresh knows how to load just enough of the content from the server before applying the client-side routing logic to that base page.

This is not guaranteed to be the only way to do this, just the one that worked for me.

**Goals:**
  
1. Static files to live in "Assets" instead of "wwwroot"
  
2. Client-side routes like ~/configure/userScenarios to return ~/index.html when the browser loads them
  
3. No extra work to remember when I add new configuration pages client-side

## Program.cs – Rename WebRoot

In my Program.cs file, I renamed wwwroot to Assets:

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseKestrel()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseWebRoot("Assets")
        .UseIISIntegration()
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .Build();

    host.Run();
}
```
## Startup.cs – Default Files, Assets, Client Routes

Then in my Startup.cs file I added configuration to load "index.html" by default, static files in my "Assets" folder, and URL rewriting to rewrite client-side route patterns to the base "index.html" file:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
   // ...

    // rewrite client-side routes to return index.html
    var options = new RewriteOptions()
        .AddRewrite("^testRuns.*", "index.html", skipRemainingRules: true)
        .AddRewrite("^configure/.*", "index.html", skipRemainingRules: true)
        .AddRewrite("^settings/.*", "index.html", skipRemainingRules: true);
    app.UseRewriter(options);

    // index.html is the default if a file isn't asked for
    app.UseDefaultFiles(new DefaultFilesOptions()
    {
        DefaultFileNames = new List<string>() { "index.html" },
        FileProvider = new PhysicalFileProvider(Path.Combine(Directory.GetCurrentDirectory(), @"Assets")),
        RequestPath = new PathString("")
    });

    // and all the rest of my static files live in Assets too
    app.UseStaticFiles(new StaticFileOptions()
    {
        FileProvider = new PhysicalFileProvider(Path.Combine(Directory.GetCurrentDirectory(), @"Assets")),
        RequestPath = new PathString("")
    });

    // ...
}
```
I needed two Nuget packages:

  * Install-Package Microsoft.AspNetCore.Rewrite
  * Install-Package Microsoft.AspNetCore.StaticFiles

And the only ongoing work as I add to my application is when I add a new client-side route pattern for a new set of pages.