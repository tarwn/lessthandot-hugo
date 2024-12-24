---
title: Forking nuget to solve the proxy problem
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-13T07:46:00+00:00
ID: 1001
excerpt: |
  Introduction
  
  At work we have a squid proxy and nuget couldn't connect to the internet because of it. That was a major bummer. 
  The problem was not very new but hard to solve for someone who does not have a proxy to hide behind. There was already a d&hellip;
url: /index.php/desktopdev/mstech/forking-nuget-to-solve-the/
views:
  - 5370
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'
  - nuget
  - vb.net

---
# Introduction

At work we have a squid proxy and [nuget][1] couldn&#8217;t connect to the internet because of it. That was a major bummer.
  
The problem was not very new but hard to solve for someone who does not have a proxy to hide behind. There was already a [discussion][2] going on.
  
So I set about creating a fork.

## On my way

First thing to do was to create a fork. Which is easy as pie, just click the create fork link and wait a minute. Then I had to install [mercurial][3] since that is what codeplex uses.

After that I cloned my fork locally with <code class="codespan">hg clone https://hg01.codeplex.com/forks/chrissie1/chrissie1</code>. This is the first time I used Mercurial but it is similar to Git.

After that I had a lot of code.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nuget/nuget2.png?mtime=1294864869"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nuget/nuget2.png?mtime=1294864869" width="774" height="332" /></a>
</div>

As with all projects you fall into it seems a bit daunting to find what you need. But the code for nuget is pretty decent and finding what I needed to change was pretty easy. 

I found out that the code I would probably needed to change was in httpclient.

```csharp
using System;
using System.Net;
using System.Net.Cache;

namespace NuGet {
    public class HttpClient : IHttpClient {
        public string UserAgent {
            get;
            set;
        }

        public WebRequest CreateRequest(Uri uri) {
            WebRequest request = WebRequest.Create(uri);
            InitializeRequest(request);
            return request;
        }

        public void InitializeRequest(WebRequest request) {
            var httpRequest = request as HttpWebRequest;
            if (httpRequest != null) {
                httpRequest.UserAgent = UserAgent;
            }
            request.CachePolicy = new HttpRequestCachePolicy();
            request.UseDefaultCredentials = true;
            if (request.Proxy != null) {
                // If we are going through a proxy then just set the default credentials
                request.Proxy.Credentials = CredentialCache.DefaultCredentials;
            }
        }

        public Uri GetRedirectedUri(Uri uri) {
            WebRequest request = CreateRequest(uri);
            using (WebResponse response = request.GetResponse()) {
                if (response == null) {
                    return null;
                }
                return response.ResponseUri;
            }
        }
    }
}
```
Since I did not think that I should build the complete project to test my usecase I just planned on building a testproject with the httpclient and a testclass to test my theories. I also do this because I think I might need to ask the user for his credentials instead of taking them from the cache. But first I have to know what errors it produces.

And this was my testharness.

```csharp
using System;
using System.Data;
using NuGet;

namespace Nuget
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new HttpClient();
            var webReq = client.CreateRequest(new Uri("http://wiki.lessthandot.com/index.php/Special:WikiFeeds/rss/newestarticles"));
            try
            {
                var stream = webReq.GetResponse().GetResponseStream();
                var dat = new DataSet();
                Console.WriteLine("reading stream");
                dat.ReadXml(stream);
                Console.WriteLine("writing stream");
                Console.WriteLine(dat.Tables[0].Rows[0].ItemArray[0]);
                Console.WriteLine(dat.Tables[0].Rows[0].ItemArray[1]);
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.GetType().Name + ": " + ex.Message);
            }
            Console.ReadLine();
        }
    }
}
```
Running this the first time (the original code) gave me an Exception.

> WebException: The remote server returned an error: (407) Proxy Authentication Required.

So I put a breakpoint on the line that deals with the proxy and should get the credentials out of the cache.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nuget/nuget3.png?mtime=1294907307"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nuget/nuget3.png?mtime=1294907307" width="769" height="179" /></a>
</div>

Now I&#8217;m pretty sure that cache is related to what IE uses. So I got IE from under the dust and connected to the internet. And tried again.

And here is the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nuget/nuget3.png?mtime=1294907307"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/nuget/nuget3.png?mtime=1294907307" width="769" height="179" /></a>
</div>

I just used the same screenshot as before since their was no difference ;-).

I also tried.

```csharp
request.Proxy.Credentials = CredentialCache.DefaultNetworkCredentials;```
With the same result.

So I tried something I know that works for me.

```csharp
request.Proxy.Credentials = new NetworkCredential(username,password);```
And that worked. But that would require a major change to the code, because at some point the system will have to ask for the credentials.

SO I came up with this solution.

if (request.Proxy != null)
  
{
    
// If we are going through a proxy then just set the default credentials
    
request.Proxy.Credentials = CredentialCache.DefaultCredentials;
    
// if the defaultcredentials are empty we will have to provide them
    
if (String.IsNullOrEmpty(request.Proxy.Credentials.GetCredential(request.RequestUri,&#8221;NTLM&#8221;).UserName))
    
{
      
request.Proxy.Credentials = new NetworkCredential(settings.Username, settings.password);
    
}
  
} 

I&#8217;m pretty sure the above will not break the NTLM authentication but I can&#8217;t test.

but I still need to test it behind a none-proxy and I will have to either make it possible to store the username and password in a config file somewhere or to ask for them on connecting. Asking for them would be the more difficult option. Making it a setting somewhere would be eassier.

Not sure yet if this solved the problem, it did for my little usecase but I could have broken everything and I would need to install Moles to write decent tests, but then again I have no idea what an NTML-enabled proxy will return. 

But at least I tried.

 [1]: http://nuget.codeplex.com/
 [2]: http://nuget.codeplex.com/Thread/View.aspx?ThreadId=227886
 [3]: http://mercurial.selenic.com/