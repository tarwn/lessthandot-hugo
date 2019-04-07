---
title: SPA Routing in ASP.Net Core
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2017-06-30T11:58:40+00:00
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
  
1. Static files to live in &#8220;Assets&#8221; instead of &#8220;wwwroot&#8221;
  
2. Client-side routes like ~/configure/userScenarios to return ~/index.html when the browser loads them
  
3. No extra work to remember when I add new configuration pages client-side

## Program.cs &#8211; Rename WebRoot

In my Program.cs file, I renamed wwwroot to Assets:

<pre>public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseKestrel()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseWebRoot("Assets")
        .UseIISIntegration()
        .UseStartup<Startup&gt;()
        .UseApplicationInsights()
        .Build();

    host.Run();
}</pre>

## Startup.cs &#8211; Default Files, Assets, Client Routes

Then in my Startup.cs file I added configuration to load &#8220;index.html&#8221; by default, static files in my &#8220;Assets&#8221; folder, and URL rewriting to rewrite client-side route patterns to the base &#8220;index.html&#8221; file:

<pre>public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
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
        DefaultFileNames = new List<string&gt;() { "index.html" },
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
}</pre>

I needed two Nuget packages:

  * Install-Package Microsoft.AspNetCore.Rewrite
  * Install-Package Microsoft.AspNetCore.StaticFiles

And the only ongoing work as I add to my application is when I add a new client-side route pattern for a new set of pages.