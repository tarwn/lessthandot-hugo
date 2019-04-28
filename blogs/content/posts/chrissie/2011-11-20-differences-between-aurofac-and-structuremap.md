---
title: Differences between Autofac and Structuremap with VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-20T15:52:00+00:00
ID: 1392
excerpt: Time to try Autofac and see how it differs from structuremap.
url: /index.php/desktopdev/mstech/differences-between-aurofac-and-structuremap/
views:
  - 2776
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - autofac
  - structuremap
  - vb.net

---
So after [deciding to learn Autofac][1].

First of all I made a few classes and an interface.

```vbnet
Public Interface Interface1
    
End Interface

Public Class Class1
    Implements Interface1

    Public Sub New()
        Console.WriteLine("Instantiated Class1")
    End Sub

End Class

Public Class Class2

    Public Sub New()
        Console.WriteLine("Instantiated Class2")
    End Sub

End Class

Public Class Class3
    Public Sub New(ByVal i1 As Interface1)
        Me.I1 = i1
        Console.WriteLine("Instantiated Class3")
    End Sub

    Public Property I1 As Interface1
End Class```
And then I wrote some tests using structuremap.

```vbnet
Imports NUnit.Framework
Imports StructureMap

&lt;TestFixture()&gt;
Public Class StructureMapVB

    &lt;SetUp()&gt;
    Public Sub Setup()
        ObjectFactory.Configure(Sub(x)
                                    x.For(Of Interface1).Use(Of Class1)()
                                End Sub)
    End Sub

    &lt;Test()&gt;
    Public Sub CanInterface1BeResolved()
        Assert.IsNotNull(ObjectFactory.GetInstance(Of Interface1))
        Assert.IsInstanceOf(Of Class1)(ObjectFactory.GetInstance(Of Interface1))
    End Sub

    &lt;Test()&gt;
    Public Sub CanClass2BeResolved()
        Assert.IsNotNull(ObjectFactory.GetInstance(Of Class2))
    End Sub

    &lt;Test()&gt;
    Public Sub CanClass3BeResolved()
        Assert.IsNotNull(ObjectFactory.GetInstance(Of Class3))
    End Sub

    &lt;Test()&gt;
    Public Sub IfInterface1IsInjectedInClass3ViaConstructor()
        Assert.IsNotNull(ObjectFactory.GetInstance(Of Class3).I1)
    End Sub
End Class
```
And some tests with Autofac.

```vbnet
Imports Autofac
Imports NUnit.Framework

&lt;testFixture()&gt;
Public Class AutofacTests
    Private _builder As ContainerBuilder
    Private _container As IContainer

    &lt;SetUp()&gt;
    Public Sub Setup()
        _builder = New ContainerBuilder()
        _builder.RegisterType(Of Class1).As(Of Interface1)()
        _builder.RegisterType(Of Class2)()
        _builder.RegisterType(Of Class3)()
        _container = _builder.Build
    End Sub

    &lt;Test()&gt;
    Public Sub CanInterface1BeResolved()
        Assert.IsNotNull(_container.Resolve(Of Interface1))
        Assert.IsInstanceOf(Of Class1)(_container.Resolve(Of Interface1))
    End Sub

    &lt;Test()&gt;
    Public Sub CanClass2BeResolved()
        Assert.IsNotNull(_container.Resolve(Of Class2))
    End Sub

    &lt;Test()&gt;
    Public Sub CanClass3BeResolved()
        Assert.IsNotNull(_container.Resolve(Of Class3))
    End Sub

    &lt;Test()&gt;
    Public Sub IfInterface1IsInjectedInClass3ViaConstructor()
        Assert.IsNotNull(_container.Resolve(Of Class3).I1)
    End Sub

End Class```
Some things to note.
  
Autofac works with the client profile and Structuremap doesn&#8217;t.
  
In Autofac I needed to register Class2 and Class3 explicitly. Structuremap just instantiates them without question.
  
I used the static class for structuremap but I could have used the non-static one. 

So for simple things both IoC containers are pretty similar in usage, allthough I think Autofac seems a bit more logical.

 [1]: /index.php/DesktopDev/MSTech/picking-a-new-ioc-container