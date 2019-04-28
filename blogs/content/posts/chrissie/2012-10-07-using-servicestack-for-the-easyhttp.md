---
title: Using servicestack for the easyhttp integration tests
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-07T05:41:00+00:00
ID: 1744
excerpt: "Easyhttp uses couchfb for it's integration tests. I changed it to use servicestack instead."
url: /index.php/desktopdev/mstech/using-servicestack-for-the-easyhttp/
views:
  - 10115
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - easyhttp
  - servicestack

---
## Introduction

Easyhttp uses couchdb for it&#8217;s integration tests and this is annoying. You have to install couchdb on your machine and you have to have it running whenever you want to run the tests. 

It would be so much better if you could just fork Easyhttp and just run the tests without having to do anything special. 

And there comes ServiceStack to the rescue. ServiceStack can run standalone in a Console app or even in a service. We will use the ServiceStack approach.

## ServiceStack

You can find out all about [self hosting ServiceStack][1] on their wiki.

<span class="MT_red">Be aware that you can get an Access Denied if you try to run this on ports below 1024.</span>

Now we just have to integrate this in ourtestproject.

First we need to configure our host.

```csharp
using System.Net;
using ServiceStack.Common.Web;
using ServiceStack.ServiceInterface;
using ServiceStack.WebHost.Endpoints;

namespace EasyHttp.Specs.Helpers
{
    public class Hello
    {
        public string Name { get; set; }
    }

    public class HelloResponse
    {
        public string Result { get; set; }
    }

    public class Files
    {
        public string Name { get; set; }
    }

    public class FilesResponse
    {
        public string Result { get; set; }
    }

    public class HelloService : RestServiceBase&lt;Hello&gt;
    {
        public override object OnGet(Hello request)
        {
            return new HelloResponse { Result = "Hello, " + request.Name };
        }

...

    //Define the Web Services AppHost
    public class ServiceStackHost : AppHostHttpListenerBase
    {
        public ServiceStackHost() : base("StarterTemplate HttpListener", typeof(HelloService).Assembly) { }

        public override void Configure(Funq.Container container)
        {
            Routes
                            .Add&lt;Hello&gt;("/hello")
                            .Add&lt;Hello&gt;("/hello/{Name}");
            Routes.Add&lt;Files&gt;("/fileupload/{Name}");
        }
    }
}```
I shortened the code above. 

The most important thing here is that we need a class that inherits from AppHostHttpListenerBase. And of course you need a response, request and service class.

Just basic ServiceStack configurarion.

Then we need a class that starts our host and closes it.

```csharp
namespace EasyHttp.Specs.Helpers
{
    public class InitAndTearDownServiceStackHost
    {
        private ServiceStackHost _appHost;
        private readonly int _port;

        public InitAndTearDownServiceStackHost(int port)
        {
            _port = port;
        }

        public void Init()
        {
            var listeningOn = "http://localhost:" + _port + "/";
            _appHost = new ServiceStackHost();
            _appHost.Init();
            _appHost.Start(listeningOn); 
        }

        public void TearDown()
        {
            _appHost.Dispose();
        }
    }
}```
This class takes a port as parameter. And why is that I hear you ask. I need my tests to be able to run in isolation and I need them to be able to run in a multithreaded environment. And it&#8217;s for the multithreaded thing that I give the port as a parameter. 

Since starting and stopping your host for a test run is not really possible as far as I know, I needed to start and stop a host for each test or for each fixture. different hosts need different ports to listen on so that&#8217;s why they take the port number as a parameter. 

Maybe that we can get away with one host sometime in the future. 

## Easyhttp

Easyhttp uses mspec for it&#8217;s tests (yuck). So I needed to add the starting of the host in the context. And make sure each test got it&#8217;s unique portnumber (I will solve this with a static method in the future). 

```csharp
Establish context = () =&gt;
        {
            appHost = new InitAndTearDownServiceStackHost(16009);
            appHost.Init();
            
            httpClient = new HttpClient();
            httpClient.Request.Accept = HttpContentTypes.ApplicationJson;
        };```
And we also have to make sure our host gets disposed at the end of the run.

```csharp
Cleanup cl = () =&gt; appHost.TearDown();```
And now our tests run without getting in each others way.

## Conclusion

There is plenty room for improvement but the basics are there. Thanks to Demis Bellot for the help.

 [1]: https://github.com/ServiceStack/ServiceStack/wiki/Self-hosting