---
title: 'VB.Net: Check if an Event Gets Raised By a Method'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-08-29T10:09:04+00:00
ID: 117
url: /index.php/desktopdev/mstech/vb-net-check-if-an-event-gets-raised-by/
views:
  - 3467
categories:
  - Microsoft Technologies
tags:
  - nunit
  - rhino mocks
  - unittest
  - vb.net

---
Let&#8217;s say we want to test to see if our object really throws that event when we need it to.

```vbnet
Public Class EventThrower
  Public Event throwme(byval somestring as String)

  Public Sub SomeSub()
    RaiseEvent throwme("Yeepie!")
  End Sub
End Class
```
So when calling a method SomeSub an event throwme will be raised.

Now we make a little unittest class that looks like this.

```vbnet
Imports NUnit.Framework
Imports Rhino.Mocks

&lt;TestFixture()&gt; _
Public Class TestEvent

#Region " Private members "
        Private _Mocker As MockRepository
#End Region

#Region " Setup and TearDown "
        &lt;Setup()&gt; _
        Public Sub Setup()
            _Mocker = New MockRepository
        End Sub

        &lt;TearDown()&gt; _
        Public Sub TearDown()
            _Mocker.ReplayAll()
            _Mocker.VerifyAll()
        End Sub
#End Region

#Region " Tests "
        &lt;Test()&gt; _
        Public Sub Test_NewFile_If_RaisesEvent_NewFileMade()
            Dim subscriber As ISubscriber = _Mocker.DynamicMock(Of ISubscriber)()
            Dim _EventThrower As New EventThrower
            AddHandler _EventThrower.throwme, AddressOf subscriber.throwmesub
            subscriber.throwmesub(Nothing)
            LastCall.IgnoreArguments()
            _Mocker.ReplayAll()
            _EventThrower.SomeSub()
        End Sub
#End Region


        Public Interface ISubscriber
            Sub throwmesub(ByVal somestring As String)
        End Interface

    End Class

    
End Namespace
```
Just a few notes. I created an interface just for this test. And interface with a method with the same signature as the event. Then in the Test, I subscribe the event to that method. I didn&#8217;t forget to set IgnoreArguments and I called the Method on our object. 

If the test goes green, we know that the event is raised when we call the sub. Now it&#8217;s just a matter of subscribing to that event.