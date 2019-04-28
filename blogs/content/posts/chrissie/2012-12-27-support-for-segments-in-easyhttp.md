---
title: Support for segments in easyhttp
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-27T05:54:00+00:00
ID: 1884
excerpt: Added a feature to easyhttp to make it work with Nancy a bit better.
url: /index.php/webdev/serverprogramming/support-for-segments-in-easyhttp/
views:
  - 1494
categories:
  - ASP.NET
  - Server Programming
tags:
  - 'c#'
  - easyhttp

---
## Introduction

Because [nancy does not seem to support][1] named url parameters but only url segments and easyhttp makes it&#8217;s urls with named parameters only, I decided to add this feature to easyhttp.

Url with named parameters 

http://localhost/trees?Id=1

Url with segments

http://localhost/trees/1

## How it works

You used to be able to do this.

```csharp
var http = new HttpClient();
http.Request.Accept = HttpContentTypes.ApplicationJson;
var result = http.Get("http://localhost/trees", new {Id = 1} );```
Which resulted in easyhttp creating a url for you that looked like this.

http://localhost/trees?Id=1

From version 1.6.30.0 onwards you can do this.

```csharp
var http = new HttpClient();
http.Request.Accept = HttpContentTypes.ApplicationJson;
http.Request.ParametersAsSegments = true;
var result = http.Get("http://localhost/trees", new {Id = 1});```
Note that the line http.Request.ParametersAsSegments is now true. That property is false by default, as one would expect from a boolean.

Which will result in a url looking like this.

http://localhost/trees/1

Note that the order of the propertie in the object you are passing is important.

```csharp
var result = http.Get("http://localhost/trees", new {Id = 1, Name = "test"});```
Will result in.

http://localhost/trees/1/test

While

```csharp
var result = http.Get("http://localhost/trees", new {Name = "test", Id = 1});```
Will result in.

http://localhost/trees/test/1

## Conclusion

Thanks to Hadi for letting me do this. 

<span class="MT_red">PS:</span> I added a whole lot of documentation to easyhttp in [the github wiki][2].
  
<span class="MT_red">PS2:</span> And I want to also note that I don&#8217;t like markdown.
  
<span class="MT_red">PS3:</span> And that all the code in this post is C#.

 [1]: /index.php/WebDev/ServerProgramming/nancy-and-vb-net-using
 [2]: https://github.com/hhariri/EasyHttp/wiki