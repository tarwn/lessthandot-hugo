---
title: 'VB.Net: Setter injection with StructureMap and the FI'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-22T10:32:30+00:00
ID: 148
url: /index.php/desktopdev/mstech/vb-net-setter-injection-with-structurema/
views:
  - 13744
categories:
  - Microsoft Technologies
tags:
  - setterinjection
  - structuremap
  - vb.net

---
I&#8217;m using the SVN version for this.

Lets give an example of how to do this.

First we create a few interfaces.

```vbnet
Public Interface IPerson
    Function Name() As String
End Interface```
```vbnet
Public Interface ITest
    Function Name() As String
    Property Person() As IPerson
End Interface```
as we can see ITest has a property Person of type IPerson. Ideally we would like to inject the IPerson.

Then we have the concrete classes.

```vbnet
Public Class Person
    Implements IPerson

    Public Function Name() As String Implements IPerson.Name
        Return "Person"
    End Function
End Class```
```vbnet
Public Class Test
    Implements ITest

    Private _Person As IPerson

    Public Sub New()

    End Sub

    Public Function Name() As String Implements ITest.Name
        Return "test"
    End Function

    Public Property Testprop() As IPerson Implements ITest.Person
        Get
            Return _Person
        End Get
        Set(ByVal value As IPerson)
            _Person = value
        End Set
    End Property
End Class```
Now I want to inject the Person that I have already made with structureMap.

```vbnet
Dim _Registry As StructureMap.Configuration.DSL.Registry
_Registry.ForRequestedType(Of IPerson).TheDefault.Is.OfConcreteType(Of Person).WithName("default")
_Registry.ForRequestedType(Of ITest).TheDefault.Is.OfConcreteType(Of Test).WithName("default").SetterDependency(Of IPerson).Is(Function(e) e.TheDefault)
Dim _Container As New StructureMap.IContainer(_Registry)```
The first line makes a new registry.
  
The second line adds a Iperson to the registry with as concretetype Person that is also the default for IPerson with a name of default.
  
The third line adds an ITest to the registry with as concretetype Test that is also the default for ITest with a name of default and a setterdepency on IPerson that wants the default implementation of IPerson which we declared in line 2.
  
Line 4 creates the container.

Having done all this we can now see the result via console.writeline (our favorite testrunner ;-))

```vbnet
Console.WriteLine(_container.GetInstance(Of ITest).Name)
        Console.WriteLine(_container.GetInstance(Of ITest).Person.Name)
        Console.WriteLine(_container.GetInstance(Of IPerson).Name)```
This should give the following as a result.

> test
  
> Person
  
> Person

Simple enough. Very usefull for when you want to inject your observable into a usercontrol. Usercontrols should have a default constructor for the designer, actually it is also the only constructor because the designer can have a difficult time otherwise. This is actually the only reason why I use Setterinjection instead of constructorinjection.