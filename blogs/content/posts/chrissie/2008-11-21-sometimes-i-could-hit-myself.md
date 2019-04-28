---
title: Sometimes I could hit myself.
author: Christiaan Baes (chrissie1)
type: post
date: 2008-11-21T07:38:35+00:00
ID: 214
url: /index.php/desktopdev/mstech/sometimes-i-could-hit-myself/
views:
  - 3526
categories:
  - Microsoft Technologies
tags:
  - showdialog
  - vb.net
  - windows forms

---
Of course I would never hit a beautiful, charming and intelligent man like myself but I sometimes think about it. Especially if I do stupid things like this.

```vbnet
Public Shadows Sub ShowDialog(ByVal Parent As System.Windows.Forms.Form) Implements Forms.Interfaces.Ifrm.ShowDialog
            MyBase.ShowDialog(Me)
        End Sub```
You get this error if you do that.

> System.ArgumentException: Form showDialog tried to set an ineligible form as its owner. Forms cannot own themselves or their owners.
  
> Parameter name: owner

Makes perfect sense. So don&#8217;t ever do that again. The good thing is that there is an exception for this, so someone did this before.