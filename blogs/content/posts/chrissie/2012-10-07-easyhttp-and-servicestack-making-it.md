---
title: Easyhttp and servicestack, making the mspec tests better
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-07T09:18:00+00:00
ID: 1745
excerpt: Where I explain on how to make the previous implementation better.
url: /index.php/desktopdev/mstech/easyhttp-and-servicestack-making-it/
views:
  - 7083
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - easyhttp
  - servicestack

---
In [my previous post][1] I showed you how I used ServiceStack to do the integration tests with Easyhttp. 

I also used a different port for each test and thus a different instance of ServiceStack for each of these tests.

Of course The Boss was not happy with this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/EasyhttpServiceStack1.png?mtime=1349606247"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/EasyhttpServiceStack1.png?mtime=1349606247" width="319" height="173" /></a>
</div>

So I went for a look on how to do this the better way.

And I found [this on Stackoverflow][2]. So thanks to Jason Watts for helping me along.

So the ServiceStackHost.cs file is still the same as before. But I created this class.

```csharp
using Machine.Specifications;

namespace EasyHttp.Specs.Helpers
{
    public class DataSpecificationBase : IAssemblyContext
    {
        private ServiceStackHost _appHost;
        private int _port;

        void IAssemblyContext.OnAssemblyComplete()
        {
            _appHost.Dispose();    
        }

        void IAssemblyContext.OnAssemblyStart()
        {
            _port = 16000;
            var listeningOn = "http://localhost:" + _port + "/";
            _appHost = new ServiceStackHost();
            _appHost.Init();
            _appHost.Start(listeningOn); 
        }

    }
}```
And I removed the apphost field and init and teardwon code from the tests. So a test now looks like this.

```csharp
[Subject("HttpClient")]
    public class when_making_a_GET_request_with_valid_uri
    {
        Establish context = () =&gt;
        {
            httpClient = new HttpClient();
        };

        Because of = () =&gt;
        {
            httpResponse = httpClient.Get("http://localhost:16000");

        };

        It should_return_body_with_rawtext =
            () =&gt; httpResponse.RawText.ShouldNotBeEmpty();

    
        static HttpClient httpClient;
        static HttpResponse httpResponse;
    }```
It has no clue about apphost.

And now the Boss is happy.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/EasyhttpServiceStack2.png?mtime=1349608345"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/servicestack/EasyhttpServiceStack2.png?mtime=1349608345" width="320" height="213" /></a>
</div>

And you can find all [the code on Github][3].

So now I know a bit more about mspec, servicestack and easyhttp. One is never to old to learn.

 [1]: /index.php/DesktopDev/MSTech/using-servicestack-for-the-easyhttp
 [2]: http://stackoverflow.com/questions/5906174/mspec-run-generic-setup-and-tear-down-code-with-each-test
 [3]: https://github.com/hhariri/EasyHttp