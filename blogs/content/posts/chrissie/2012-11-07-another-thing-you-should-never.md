---
title: Another thing you should never do in VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2012-11-07T13:43:00+00:00
ID: 1782
excerpt: Another thing you should never do in VB.Net.
url: /index.php/desktopdev/mstech/another-thing-you-should-never/
views:
  - 2636
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - vb.net

---
There is plenty of things you should never do but can. Lot&#8217;s of people would say to never use VB.Net in the first place, but they are wrong.

Declare an event and make all the parameters have the same name for instance.

```vbnet
Public Class Class1
  Public Event NewEvent(ByVal param1 As String, ByVal param1 As String, ByVal param1 As String)

  Public Sub Doevent()
    RaiseEvent NewEvent("1", "2", "3")
  End Sub
End Class```
The above is completely legal and it makes complete sense in that context too. 

It will however not make any sense to the people that are consuming your event.

This is what will be created for you if you let VS have it&#8217;s way.

```vbnet
Public Class Class2

        Private WithEvents _class1 As Class1

        Sub New(ByVal class1 As Class1)
            _class1 = class1
        End Sub
        
        Private Sub _class1_NewEvent(param1 As String, param11 As String, param12 As String) Handles _class1.NewEvent

        End Sub
    End Class```
So be nice to people and don&#8217;t do that. If we had stylecop for VB we could make a rule for that, but alas Microsoft does not like VB developers and I&#8217;m to lazy to make it myself.