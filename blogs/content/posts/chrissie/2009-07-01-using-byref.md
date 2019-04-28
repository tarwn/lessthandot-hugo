---
title: Using Byref
author: Christiaan Baes (chrissie1)
type: post
date: 2009-07-01T11:33:36+00:00
ID: 489
url: /index.php/desktopdev/mstech/using-byref/
views:
  - 1740
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - vb.net

---
Still working on the legacy application and seeing this

```vbnet
Module Module1
  Public x as long
  
  Sub mymethod1(Byref x as long)
    x = x + 1
  End Sub

  Sub mymethod2()
    mymethod1(x)
  end sub
End Module```
Way cool.