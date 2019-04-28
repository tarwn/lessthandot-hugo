---
title: Service locator pattern, why and why not.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-07-06T15:11:46+00:00
ID: 836
url: /index.php/desktopdev/mstech/service-locator-pattern-why-and-why-not/
views:
  - 7143
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - dependency injection
  - inversion of control
  - service locator pattern
  - vb.net

---
## Introduction

When you use a DI/IoC-Container like StructureMap you will be using the Service Locator pattern. On [MSDN][1] you can find a pretty good explanation of the Service Locator pattern so I won&#8217;t repeat that here. You will also note they have no examples for VB.Net. I guess they think this pattern is too complicated for us VB.Net developers. But it isn&#8217;t. Also worth reading are [Dependency Injection][2] and [Inversion of Control][3]

## The problem with Service Locator pattern

Let me repeat what [MSDN][1] has to say.

> Liabilities
> 
> The Service Locator pattern has the following liabilities:
> 
> * There are more solution elements to manage.
      
> * You have to write additional code to add service references to the locator before your objects use it.
      
> * Your classes have an extra dependency on the service locator.
      
> * The source code has added complexity; this makes the source code more difficult to understand.

So they are saying it is more complex. Well perhaps, but only very little if you do it right. The whole thing about the Service Locator pattern is that people will overuse it when they start using the DI/IoC-container. It is all too easy to replace this kind of code

```vbnet
Public Class Controller

  Private _View as IView

  Public Sub New()
    _View = new Form1()
  End Sub
End Class```
```csharp
public class Controller

  IView _View;

  public Controller()
  {
    _View = new Form1();
  }
}```
with this kind of code 

```vbnet
Public Class Controller

  Private _View as IView

  Public Sub New()
    _View = Container.Resolve(Of IView)()
  End Sub
End Class```
```csharp
public class Controller

  IView _View;

  public Controller()
  {
    _View = Container.Resolve&lt;IView&gt;();
  }
}```
Resolve being a shared method here. 

> In StructureMap this would be Objectfactory.GetInstance(Of &#8230;)/ObjectFactory.GetInstance<...>(); in Unity it is called container.Resolve(Of &#8230;)/Container.Resolve<...>(); in castle windsor it is also Resolve and in Spring I think it is GetObject()

You will then end up with hundreds of these calls to Container.Resolve. The advantage of this is that you can still easily replace an implementation with another implementation. But you are also creating a great dependency on your container. So, although better then before, it is not the best choice you have. 

The following option is much better. Injecting the dependency into the class via the constructor.

```vbnet
Public Class Controller

  Private _View as IView

  Public Sub New(ByVal View as IView)
    _View = View
  End Sub
End Class```
```csharp
public class Controller

  IView _View;

  public Controller(IView View)
  {
    _View = IView;
  }
}```
Or in some cases injecting the dependency via a property.

```vb.net
Public Class Controller

  Private _View as IView

  Public Property View as IView
    Get
      return _View
    End Get
    Set(Byval value as IView)
      _View = value
    End Set
  End Property
  
  Public Sub New()
  
End Sub
End Class```
```csharp
public class Controller

  IView _View;

  public int View
  {
    get
    {
      return _View;
    }
    set
    {
      _View = value;
    }
  }

  public Controller()
  {
  }
}```
The big advantage is that you no longer have any dependency on your container.
  
You are now using the DI of a DI/IoC container like it was meant to be used. 

## The problems with Dependency injection

When you do the Dependency injection via the Constructor thing and you end up with the perfect situation, where you have one Container.Resolve in your code and all the rest gets injected automagically by the container, you would think you are ready for the big time. But don&#8217;t be mistaken most containers won&#8217;t do lazy loading and they will make all your classes when you do the resolve the first time. This could make for a very slow load the very first time. This might or might not be a problem but at least something to think about. 

## Conclusion

So be careful with the Service Locator pattern, don&#8217;t over use it when working with a container. Use dependency injection instead but be wary of performance if you do. In other words, think before you do.

 [1]: http://msdn.microsoft.com/en-us/library/ff649658.aspx
 [2]: http://msdn.microsoft.com/en-us/library/ff649378.aspx
 [3]: http://msdn.microsoft.com/en-us/library/ff647976.aspx