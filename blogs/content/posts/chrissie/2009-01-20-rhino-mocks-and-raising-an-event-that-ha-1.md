---
title: Rhino mocks and raising an event that has parameters and the AAA syntax the VB.Net version
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-20T08:06:44+00:00
ID: 291
url: /index.php/desktopdev/mstech/rhino-mocks-and-raising-an-event-that-ha-1/
views:
  - 4020
categories:
  - Microsoft Technologies
tags:
  - aaa syntax
  - rhino mocks
  - vb.net

---
This is the VB.Net version of [&#8220;Rhino mocks and raising an event that has parameters and the AAA syntax&#8221;][1] which was the C# version. I&#8217;m not saying it&#8217;s perfect but it works. Well most things are pretty good except the AddingHandler functions. I&#8217;m pretty sure I could get that down to one function. But I have spent enough time on this already. All tests pass.

And here is the code. The explenation is pretty much the same as the previous post. Excpet for some VB.Net lambda weirdness we have to live with.

```vbnet
Imports System
Imports NUnit.Framework
Imports Rhino.Mocks

Public Interface IEventsRaiser
    Event event1()
    Event event2(ByVal test As String)
    Event event3(ByVal test1 As String, ByVal test2 As Integer)
    Event event4(ByVal test As Object)
    Event event5(ByVal test1 As Object, ByVal test2 As Object)
    Event event6(ByVal test1 As Object, ByVal test2 As Object, ByVal test3 As Object)
End Interface

Public Class EventRaiser
    Implements IEventsRaiser
    Public Event event1() Implements IEventsRaiser.event1
    Public Event event2(ByVal test As String) Implements IEventsRaiser.event2
    Public Event event3(ByVal test1 As String, ByVal test2 As Integer) Implements IEventsRaiser.event3
    Public Event event4(ByVal test As Object) Implements IEventsRaiser.event4
    Public Event event5(ByVal test1 As Object, ByVal test2 As Object) Implements IEventsRaiser.event5
    Public Event event6(ByVal test1 As Object, ByVal test2 As Object, ByVal test3 As Object) Implements IEventsRaiser.event6
End Class

Public Class EventConsumer

    Private WithEvents eventraiser As IEventsRaiser
    Private _message As String

    Public Sub New(ByVal eventraiser As IEventsRaiser)
        Me.eventraiser = eventraiser
   End Sub

    Public Property Message() As String
        Get
            Return _message
        End Get
        Set(ByVal value As String)
            _message = value
        End Set
    End Property


    Public Sub Eventhandler1() Handles eventraiser.event1
        Message = "Eventhandler1"
    End Sub

    Public Sub Eventhandler2(ByVal test As String) Handles eventraiser.event2
        Message = "Eventhandler2"
    End Sub

    Public Sub Eventhandler3(ByVal test1 As String, ByVal test2 As Integer) Handles eventraiser.event3
        Message = "Eventhandler3"
    End Sub

    Public Sub Eventhandler4(ByVal test As Object) Handles eventraiser.event4
        Message = "Eventhandler4"
    End Sub

    Public Sub Eventhandler5(ByVal test1 As Object, ByVal test2 As Object) Handles eventraiser.event5
        Message = "Eventhandler5"
    End Sub

    Public Sub Eventhandler6(ByVal test1 As Object, ByVal test2 As Object, ByVal test3 As Object) Handles eventraiser.event6
        Message = "Eventhandler6"
    End Sub
End Class

&lt;TestFixture()&gt; _
Public Class TestRaiseEvents
    Private eventraiser As IEventsRaiser
    Private eventsConsumer As EventConsumer

    &lt;SetUp()&gt; _
    Public Sub SetUp()
        eventraiser = MockRepository.GenerateMock(Of IEventsRaiser)()
        eventsConsumer = New EventConsumer(eventraiser)
    End Sub

    &lt;Test()&gt; _
    Public Sub Test_raising_event1_without_parameters_using_raise()
        Assert.IsNull(eventsConsumer.Message)
        eventraiser.Raise(Function(e) AddingHandlerEvent1(e))
        Assert.AreEqual("Eventhandler1", eventsConsumer.Message)
    End Sub

    Private Function AddingHandlerEvent1(ByVal e As IEventsRaiser) As Boolean
        AddHandler e.event1, AddressOf eventsConsumer.Eventhandler1
        Return True
    End Function

    &lt;Test()&gt; _
    &lt;ExpectedException(ExceptionName:="System.InvalidOperationException", ExpectedMessage:="You have called the event raiser with the wrong number of parameters. Expected 0 but was 0")&gt; _
    Public Sub Test_raising_event1_without_parameters_using_geteventraiser_and_raise()
        Assert.IsNull(eventsConsumer.Message)
        eventraiser.GetEventRaiser(Function(e) AddingHandlerEvent1(e)).Raise(Nothing)
        Assert.AreEqual("Eventhandler1", eventsConsumer.Message)
    End Sub

    &lt;Test()&gt; _
    &lt;ExpectedException(ExceptionName:="System.InvalidOperationException", ExpectedMessage:="You have called the event raiser with the wrong number of parameters. Expected 1 but was 0")&gt; _
    Public Sub Test_raising_event2_with_parameters_wrongly_called()
        Assert.IsNull(eventsConsumer.Message)
        eventraiser.Raise(Function(e) AddingHandlerEvent2(e))
        Assert.AreEqual("Eventhandler2", eventsConsumer.Message)
    End Sub

    &lt;Test()&gt; _
    Public Sub Test_raising_event2_with_parameters()
        Assert.IsNull(eventsConsumer.Message)
        eventraiser.GetEventRaiser(Function(e) AddingHandlerEvent2(e)).Raise("test")
        Assert.AreEqual("Eventhandler2", eventsConsumer.Message)
    End Sub

    Private Function AddingHandlerEvent2(ByVal e As IEventsRaiser) As Boolean
        AddHandler e.event2, AddressOf eventsConsumer.Eventhandler2
        Return True
    End Function

    &lt;Test()&gt; _
    Public Sub Test_raising_event3_with_parameters()
        Assert.IsNull(eventsConsumer.Message)
        eventraiser.GetEventRaiser(Function(e) AddingHandlerEvent3(e)).Raise("test", 0)
       Assert.AreEqual("Eventhandler3", eventsConsumer.Message)
    End Sub

    Private Function AddingHandlerEvent3(ByVal e As IEventsRaiser) As Boolean
        AddHandler e.event3, AddressOf eventsConsumer.Eventhandler3
        Return True
    End Function

    &lt;Test()&gt; _
    Public Sub Test_raising_event4_with_parameters()
        Assert.IsNull(eventsConsumer.Message)
        eventraiser.GetEventRaiser(Function(e) AddingHandlerEvent4(e)).Raise(New Object)
        Assert.AreEqual("Eventhandler4", eventsConsumer.Message)
    End Sub

    Private Function AddingHandlerEvent4(ByVal e As IEventsRaiser) As Boolean
        AddHandler e.event4, AddressOf eventsConsumer.Eventhandler4
        Return True
    End Function

    &lt;Test()&gt; _
    Public Sub Test_raising_event5_with_parameters()
        Assert.IsNull(eventsConsumer.Message)
        eventraiser.GetEventRaiser(Function(e) AddingHandlerEvent5(e)).Raise(New Object, New Object)
        Assert.AreEqual("Eventhandler5", eventsConsumer.Message)
    End Sub

    Private Function AddingHandlerEvent5(ByVal e As IEventsRaiser) As Boolean
        AddHandler e.event5, AddressOf eventsConsumer.Eventhandler5
        Return True
    End Function

    &lt;Test()&gt; _
    Public Sub Test_raising_event6_with_parameters()
        Assert.IsNull(eventsConsumer.Message)
        eventraiser.GetEventRaiser(Function(e) AddingHandlerEvent6(e)).Raise(New Object, New Object, New Object)
        Assert.AreEqual("Eventhandler6", eventsConsumer.Message)
    End Sub

    Private Function AddingHandlerEvent6(ByVal e As IEventsRaiser) As Boolean
        AddHandler e.event6, AddressOf eventsConsumer.Eventhandler6
        Return True
    End Function
End Class
```

 [1]: /index.php/DesktopDev/MSTech/rhino-mocks-and-raising-an-event-that-ha