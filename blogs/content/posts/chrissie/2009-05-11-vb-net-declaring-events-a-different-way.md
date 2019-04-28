---
title: 'VB.Net: declaring events a different way'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-05-11T10:37:19+00:00
ID: 420
url: /index.php/desktopdev/mstech/vb-net-declaring-events-a-different-way/
views:
  - 1490
categories:
  - Microsoft Technologies
tags:
  - events
  - vb.net

---
Pre VB.Net 9.0 we used to write something like this to get an event that used a custom signature.

```vbnet
public Event SomethingEvents(Byval somethingToPass as String)```
Now we can also write

```vbnet
Public Event SomethingEvents As Action(Of String)```
Which is exactly the same thing but it looks so much cooler ;-).