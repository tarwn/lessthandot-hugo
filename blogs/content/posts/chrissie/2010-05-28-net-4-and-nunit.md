---
title: .Net 4 And Nunit
author: Christiaan Baes (chrissie1)
type: post
date: 2010-05-28T07:51:56+00:00
ID: 801
url: /index.php/desktopdev/mstech/net-4-and-nunit/
views:
  - 3894
categories:
  - Microsoft Technologies
tags:
  - .net 4.0
  - nunit

---
Today I decided it was time to set some of my projects to use .Net 4 instead of .Net 3.5 and I felt the pain. 

First, I had some trouble with my barcodereader framework which was built for .Net 1.1. No problem, I quickly changed it over with another framework I like better and which is built for .Net 4.0.

Then I committed and I broke the built :-(. The test projects I had converted to .net 4.0. would not build anymore.

> System.BadImageFormatException: Could not load file or assembly &#8216;C:Visual studio projectsTDB2009ProjectsTDB2009.View.Menu.TestCbinDebugTDB2009.View.Menu.TestC.dll&#8217; or one of its dependencies. This assembly is built by a runtime newer than the currently loaded runtime and cannot be loaded.

And this in the output.

> Execution Runtime: net-2.0

That won&#8217;t work.

First thing I did was to upgrade NUnit to version 2.5.5 from version 2.5.2. 

That didn&#8217;t help. Perhaps it did but not at first.

Searching google I found this.

[Getting .Net 4.0, Team City, MSBuild and Nunit to play nice.][1]

And what helped me was this line.

> Modify nunit-console.exe.config with the following:
> 
> under <code class="codespan">&lt;configuration&gt;</code> add: <code class="codespan">&lt;startup&gt;  &lt;requiredruntime version="v4.0.30319"&gt;&lt;/requiredruntime&gt;&lt;/startup&gt;&lt;/configuration&gt;</code>

GUI config. And all is well again.

 [1]: http://gojisoft.com/blog/2010/04/14/getting-net-4-0-team-city-msbuild-and-nunit-to-play-nice/