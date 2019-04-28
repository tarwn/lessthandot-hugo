---
title: 'VB.Net: Reflection and a private field marked with WithEvents'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-02-09T12:40:02+00:00
ID: 320
url: /index.php/desktopdev/mstech/vb-net-reflection-and-a-private-field-ma/
views:
  - 7482
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - reflection
  - vb.net
  - withevents

---
If you have this piece of code.

```vbnet
Public Class Test
  Private WithEvents _Obj as ObjectWithEvents

...
End Class```
And you want to get the Value of that field via reflection using C# because for some reason you just do. Trust me.

```csharp
var objectWithEvents = testinstance.GetType().GetField("_Obj", BindingFlags.NonPublic | BindingFlags.Instance).GetValue(testinstance);
            ```
Then know that the above will fail. Because VB.Net just happens to add an extra underscore to fields marked with WithEvents. So this will work.

```csharp
var objectWithEvents = testinstance.GetType().GetField("__Obj", BindingFlags.NonPublic | BindingFlags.Instance).GetValue(testinstance);
            ```
I should try if that gives the same result when reflected from VB.Net but I&#8217;m pretty certain of it, so I won&#8217;t. It could also be that I already wrote about this before. But I forgot and Google didn&#8217;t give me anything.