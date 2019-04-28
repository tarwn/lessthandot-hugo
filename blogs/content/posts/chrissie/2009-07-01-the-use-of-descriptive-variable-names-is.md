---
title: The use of descriptive variable names is forbidden
author: Christiaan Baes (chrissie1)
type: post
date: 2009-07-02T03:18:43+00:00
ID: 490
url: /index.php/desktopdev/mstech/the-use-of-descriptive-variable-names-is/
views:
  - 2368
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - vb.net

---
Still legacy application

```vbnet
For x = 1 To y
  q(x, y + 1) = q(x, y + 1) + p(I, J) * ay(I)
  For z = 1 To y
    q(z, x) = q(z, x) + p(I, z) * p(I, x)
  Next z
Next x```
I immediately refactored the I and J to be lowercase.