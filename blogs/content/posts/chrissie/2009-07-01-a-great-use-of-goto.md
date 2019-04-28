---
title: A great use of Goto
author: Christiaan Baes (chrissie1)
type: post
date: 2009-07-01T04:33:37+00:00
ID: 488
url: /index.php/desktopdev/mstech/a-great-use-of-goto/
views:
  - 1851
categories:
  - Microsoft Technologies
tags:
  - goto
  - vb.net

---
I&#8217;m in the process of upgrading a legacy VB program and I found this

```vbnet
10:
    For x as integer = 1 to whatever
      if x = 1 then goto 20
      dosomethingelse
20: Next```
I&#8217;m gonna use this more often, it&#8217;s cool.