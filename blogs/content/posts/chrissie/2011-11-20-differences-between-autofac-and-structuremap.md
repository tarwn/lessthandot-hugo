---
title: Differences between Autofac and Structuremap with VB.Net the use of Lazy(Of T)
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-20T16:55:00+00:00
ID: 1393
excerpt: One of the things that can burn you when using an IoC container is that if your objectgraph is big than it will take some time to instantiate everything at the startup of your application. But why would we not use Lazy(Of T) to prevent this? After all that is what Lazy(Of T) is for. To lazy initialize your objects.
url: /index.php/desktopdev/mstech/differences-between-autofac-and-structuremap/
views:
  - 5897
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - autofac
  - structuremap
  - vb.net

---
One of the things that can burn you when using an IoC container is that if your objectgraph is big than it will take some time to instantiate everything at the startup of your application. But why would we not use Lazy(Of T) to prevent this? After all that is what Lazy(Of T) is for. To lazy initialize your objects. 

Of course our IoC container must be able to handle this. 

Lets create some interfaces and classes to prove our theory.

```vbnet
Public Interface Interface1
    
End Interface

Public Interface Interface2
    ReadOnly Property IsI1Created As Boolean
    ReadOnly Property I1 As Interface1
End Interface

Public Class Class1
    Implements Interface1

    Public Sub New()
        Console.WriteLine("Instantiated Class1")
    End Sub

End Class

Public Class Class4
    Implements Interface2

    Private i1 As Lazy(Of Interface1)

    Public Sub New(i1lazy As Lazy(Of Interface1))
        i1 = i1lazy
    End Sub

    Public ReadOnly Property IsI1Created As Boolean Implements Interface2.IsI1Created
        Get
            Return i1.IsValueCreated
        End Get
    End Property

    Public ReadOnly Property I11 As Interface1 Implements Interface2.I1
        Get
            Return i1.Value
        End Get
    End Property
End Class```
So I have an interface2 that has an isvaluecreated and a property to get our instance of interface1.

Class4 is our implementation of interface2 and takes a dependency on interface1 via the constructor in the form of Lazy(Of T)

Testing this is simple.

Here for Autofac.

```vbnet
Imports Autofac
Imports NUnit.Framework

&lt;TestFixture()&gt;
Public Class AutofacLazyTests
    Private _builder As ContainerBuilder
    Private _container As IContainer

    &lt;SetUp()&gt;
    Public Sub Setup()
        _builder = New ContainerBuilder()
        _builder.RegisterType(Of Class1).As(Of Interface1)()
        _builder.RegisterType(Of Class4).As(Of Interface2)()
        _container = _builder.Build
    End Sub

    &lt;Test()&gt;
    Public Sub IsInterface1ValueNotCreatedOnResolve()
        Assert.IsFalse(_container.Resolve(Of Interface2).IsI1Created)
    End Sub

    &lt;Test()&gt;
    Public Sub IsInterface1ValueCreatedGettingValue()
        Assert.IsNotNull(_container.Resolve(Of Interface2).I1)
    End Sub
End Class```
And here for StructureMap.

```vbnet
Imports NUnit.Framework
Imports StructureMap

&lt;TestFixture()&gt;
Public Class StructuremapLazytest
    &lt;SetUp()&gt;
    Public Sub Setup()
        ObjectFactory.Configure(Sub(x)
                                    x.For(Of Interface1).Use(Of Class1)()
                                    x.For(Of Interface2).Use(Of Class4)()
                                End Sub)
    End Sub

    &lt;Test()&gt;
    Public Sub IsInterface1ValueNotCreatedOnResolve()
        Assert.IsFalse(ObjectFactory.GetInstance(Of Interface2).IsI1Created)
    End Sub

    &lt;Test()&gt;
    Public Sub IsInterface1ValueCreatedGettingValue()
        Assert.IsNotNull(ObjectFactory.GetInstance(Of Interface2).I1)
    End Sub
End Class```
The Autofac tests will just succeed. And the Structuremap tests just fail.

With this errormessage.

> StructureMap.StructureMapException : StructureMap Exception Code: 202
  
> No Default Instance defined for PluginFamily System.Lazy\`1[[AutofacVB.Interface1, AutofacVB, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null]], mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089

And as far as I can see there is no way around that for structuremap, but I could be wrong.

<span class="MT_red">Update</span>

Chad points out that using <code class="codespan">Func(Of T)</code> would solve the problem. 

For the above tests to then pass I would have to change my Class4 to this.

```vbnet
Namespace DefaultNamespace
    Public Class Class4
        Implements Interface2

        Private i1 As Func(Of Interface1)

        Public Sub New(i1lazy As Func(Of Interface1))
            i1 = i1lazy
        End Sub

        Public ReadOnly Property IsI1Created As Boolean Implements Interface2.IsI1Created
            Get
                Return i1 Is Nothing
            End Get
        End Property

        Public ReadOnly Property I11 As Interface1 Implements Interface2.I1
            Get
                Return i1()
            End Get
        End Property
    End Class
End Namespace```
The above proves that Structuremap can lazy load dependecies, which doesn&#8217;t change the fact that Lazy(Of T) doesn&#8217;t work with Structuremap.

And don&#8217;t get me wrong, I&#8217;m not blaming Structuremap or saying that it is bad, it&#8217;s just something to consider. It&#8217;s always great if you can choose the right tool for the job.