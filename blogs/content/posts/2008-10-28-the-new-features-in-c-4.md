---
title: 'The new features in C# 4.0'
author: SQLDenis
type: post
date: 2008-10-28T17:29:22+00:00
url: /index.php/desktopdev/mstech/the-new-features-in-c-4/
views:
  - 45097
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'

---
Microsoft has made available a document showing the new features in C# 4.0.

The new features in C# 4.0 fall into four groups:

**Dynamic lookup**
  
Dynamic lookup allows you to write method, operator and indexer calls, property and field accesses, and even object invocations which bypass the C# static type checking and instead gets resolved at runtime.

**Named and optional parameters**
  
Parameters in C# can now be specified as optional by providing a default value for them in a member declaration. When the member is invoked, optional arguments can be omitted. Furthermore, any argument can be passed by parameter name instead of position.

**COM specific interop features**
  
Dynamic lookup as well as named and optional parameters both help making programming against COM less painful than today. On top of that, however, we are adding a number of other small features that further improve the interop experience.

**Variance**
  
It used to be that an IEnumerable<string> wasn’t an IEnumerable<object>. Now it is – C# embraces type safe “co-and contravariance” and common BCL types are updated to take advantage of that.

You can download a Word document written by Mads Torgersen with a little more info here: [C# 4.0][1]

 [1]: http://code.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=csharpfuture&DownloadId=3550