---
title: Thank you VB 10 for adding better anonymous delegate support
author: Christiaan Baes (chrissie1)
type: post
date: 2010-08-02T10:48:53+00:00
ID: 859
url: /index.php/desktopdev/mstech/thank-you-vb-10-for-adding-better-anonym/
views:
  - 5033
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - anonymous delegate
  - vb.net 10.0
  - vb.net 9.0

---
## Introduction

In VB9 we already had some anonymous delegate support but only with Functions. In other words your method needed to return a value for this to work. In VB10 we also have anonymous delegate support for methods that don&#8217;t return anything (void).
  
This comes in very handy when you have to test some events and their return value.

## Prerequisites

This is the class we are going to test.

```vbnet
Public Class Messages
  Public Sub AddMessage(ByVal Message As String
    RaiseEvent MessageAdded(Message)
  End Sub

  Public Event MessageAdded(ByVal Message As String)
End Class```
The above is a very simple class. You can just image that the AddMessage method add something to a list. MessageAdded just tells the obeservers that it is time to update them selfs. Pretty simple so far.

## An example of old

This is how I would have to test this in VB9

```vbnet
&lt;TestFixture()&gt; _
Public Class TestMessages

  Private _Facade As Messages

  &lt;Test()&gt; _
  Public Sub IfAddMessageReturnsMessage()
    _Facade = New Messages()
    AddHandler _Facade.MessageAdded, AddressOf IfAddMessageReturnsMessageHandler
    _Facade.AddMessage("Test")
  End Sub

  Private Sub IfAddMessageReturnsMessageHandler(ByVal message As String)
    Assert.AreEqual("Test", message)
  End Sub
End Class
```
AS you can see I need an extra method here. If IfAddMessageReturnsMessageHandler would have been a Function it would have been easier, but eventhandlers are never Functions ;-).

## An example of new

Ans this is how we do it in VB10.

```vbnet
&lt;TestFixture()&gt; _
Public Class TestMessages

  Private _Facade As Messages

  &lt;Test()&gt; _
  Public Sub IfAddMessageReturnsMessage()
    _Facade = New Messages()
    AddHandler _Facade.MessageAdded, Sub(x) Assert.AreEqual("Test", x)
    _Facade.AddMessage("Test")
  End Sub

End Class
```
That is much cleaner and easier to read. 

## Conclusion

New Lambda support in VB10 makes life easier for us VB.Net developers. So even if you are still targeting the .Net 3.5 framework you will benefit from an upgrade to Visual Studio 2010.