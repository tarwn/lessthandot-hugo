---
title: 'StructureMap: Configure everything before using Objectfactory.GetInstance'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-07-28T12:27:09+00:00
ID: 97
url: /index.php/desktopdev/mstech/structuremap-configure-everything-before/
views:
  - 3425
categories:
  - Microsoft Technologies
  - VB.NET

---
I just found out today that you have to have everything configured in structuremap before calling getInstance. I am using structureMap 2.4.9.

To test this I created.

2 interfaces.

```vbnet
Namespace Interfaces
    Public Interface IClass1
        Sub somefunction()
    End Interface
End Namespace```
```vbnet
Namespace Interfaces
    Public Interface IClass2
        Sub someotherfunction()
    End Interface
End Namespace```
And 2 Classes.

```vbnet
Public Class Class1
    Implements Interfaces.IClass1


    Public Sub somefunction() Implements Interfaces.IClass1.somefunction
        Console.WriteLine("class1")
    End Sub
End Class
```
```vbnet
Public Class Class2
    Implements Interfaces.IClass2

    Public Sub someotherfunction() Implements Interfaces.IClass2.someotherfunction
        Console.WriteLine("class2")
    End Sub
End Class
```
And a module to run it.

```vbnet
Module Module1

    Sub Main()
        StructureMap.StructureMapConfiguration.ForRequestedType(Of Interfaces.IClass1).TheDefaultIsConcreteType(Of Class1)()
        StructureMap.ObjectFactory.GetInstance(Of Interfaces.IClass1).somefunction()
        StructureMap.StructureMapConfiguration.ForRequestedType(Of Interfaces.IClass2).TheDefaultIsConcreteType(Of Class2)()
        StructureMap.ObjectFactory.GetInstance(Of Interfaces.IClass2).someotherfunction()
    End Sub

End Module
```
After getting the class1 you will get this error.

> StructureMap Exception Code: 208 Requested type ConsoleApplication3.Interfaces.IClass2 is not configured in StructureMap

This could be a problem when you call a class-library that also uses StructureMap without you knowing. Perhaps there is a simple solution I don&#8217;t know about yet. If there is please tell me.

<span style="color:red;">Update</span>

I posted this [on the mailing list][1] as well and got a reply from Jeremy.

> Well, yeah, that is the way it works. I do need to add some more
  
> documentation for this. I&#8217;m thinking about adding some defensive
  
> checks so that if you call StructureMapConfiguration.Anything() after
  
> ObjectFactory is baked that it throws an exception.
> 
> For multiple apps/components all using StructureMap together, my
  
> strong suggestion is to put ALL configuration into Registry classes
  
> and just add all the necessary Registry&#8217;s to StructureMapConfiguration
  
> at the bootstrapping. Again, something I do intend to document.

 [1]: http://groups.google.com/group/structuremap-users/browse_thread/thread/ea3500ecd34117d2#