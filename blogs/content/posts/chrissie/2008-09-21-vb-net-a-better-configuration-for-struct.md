---
title: 'VB.Net: A better configuration for structureMap'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-21T05:19:42+00:00
ID: 147
url: /index.php/desktopdev/mstech/vb-net-a-better-configuration-for-struct/
views:
  - 7019
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - structuremap
  - vb.net

---
There used to be time when you would configure StructureMap this way

```vbnet
StructureMap.StructureMapConfiguration.ForRequestedType(Of ITest).TheDefault.Is.OfConcreteType(Of Test)()```
and then this to get your implementation

```vbnet
ObjectFactory.GetInstance(Of ITest)()```
Fine that all worked nice and dandy but it had a drawback. Structuremapconfiguration was static/shared. This meant that this.

```vbnet
StructureMap.StructureMapConfiguration.ForRequestedType(Of ITest).TheDefault.Is.OfConcreteType(Of Test)()
ObjectFactory.GetInstance(Of ITest)
StructureMap.StructureMapConfiguration.ForRequestedType(Of ITest).TheDefault.Is.OfConcreteType(Of Test)()
ObjectFactory.GetInstance(Of ITest)```
would lead to an error because you could not add classes to structuremap once objectfactory.getinstance was called. But [Jeremy and his compadres (sorry musketeers)][1] to the rescue. In the new svn version (not version 2.4.9 apparently) you can do this.

```vbnet
#Region " Private members "
        Imports TDB2007.Utils.CommonFunctions.IoC.Interfaces
Imports StructureMap

Namespace IoC.StructureMapIoC
    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Public Class StructureMapContainer
        Implements IoC.Interfaces.IContainerIoC

#Region " Private members "
''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _Container As StructureMap.IContainer
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _Registry As StructureMap.Configuration.DSL.Registry
#End Region

#Region " Constructors "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New()
            _Registry = New StructureMap.Configuration.DSL.Registry
            _Container = New StructureMap.Container(_Registry)
        End Sub
#End Region

#Region " Public methods "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="T"&gt;&lt;/typeparam&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Function Resolve(Of T)() As T Implements IContainerIoC.Resolve
            Return _Container.GetInstance(Of T)()
        End Function

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="T"&gt;&lt;/typeparam&gt;
        ''' &lt;param name="ImplamentationName"&gt;&lt;/param&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Function Resolve(Of T)(ByVal ImplamentationName As String) As T Implements IContainerIoC.Resolve
            Return _Container.GetInstance(Of T)(ImplamentationName)
        End Function

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="T"&gt;&lt;/typeparam&gt;
        ''' &lt;param name="Implemtation"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub InjectImplementation(Of T)(ByVal Implemtation As T) Implements IContainerIoC.InjectImplementation
            _Container.Inject(Of T)(Implemtation)
        End Sub

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="T"&gt;&lt;/typeparam&gt;
        ''' &lt;param name="ImplementationName"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub SetDefaultInstanceName(Of T)(ByVal ImplementationName As String) Implements IContainerIoC.SetDefaultInstanceName
            _Container.SetDefault(GetType(T), ImplementationName)
        End Sub

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="INPUTTYPE"&gt;&lt;/typeparam&gt;
        ''' &lt;typeparam name="CONCRETETYPE"&gt;&lt;/typeparam&gt;
        ''' &lt;param name="name"&gt;&lt;/param&gt;
        ''' &lt;param name="IsDefault"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub Register(Of INPUTTYPE, CONCRETETYPE As {INPUTTYPE})(Optional ByVal name As String = "default", Optional ByVal IsDefault As Boolean = True) Implements Interfaces.IContainerIoC.Register
            If IsDefault Then
                _Registry.ForRequestedType(Of INPUTTYPE).TheDefault.Is.OfConcreteType(Of CONCRETETYPE).WithName(name)
            Else
                _Registry.BuildInstancesOf(Of INPUTTYPE).AddConcreteType(Of CONCRETETYPE)(name)
            End If
            _Container = New StructureMap.Container(_Registry)
        End Sub

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;typeparam name="INPUTTYPE"&gt;&lt;/typeparam&gt;
        ''' &lt;typeparam name="CONCRETETYPE"&gt;&lt;/typeparam&gt;
        ''' &lt;param name="name"&gt;&lt;/param&gt;
        ''' &lt;param name="IsDefault"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub RegisterSingleton(Of INPUTTYPE, CONCRETETYPE As {INPUTTYPE})(Optional ByVal name As String = "default", Optional ByVal IsDefault As Boolean = True) Implements Interfaces.IContainerIoC.RegisterSingleton
            If IsDefault Then
                _Registry.ForRequestedType(Of INPUTTYPE).AsSingletons.TheDefault.Is.OfConcreteType(Of CONCRETETYPE).WithName(name)
            Else
                _Registry.ForRequestedType(Of INPUTTYPE).AsSingletons.AddConcreteType(Of CONCRETETYPE)(name)
            End If
            _Container = New StructureMap.Container(_Registry)
        End Sub

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub ResetContainer() Implements IContainerIoC.ResetContainer
            _Registry = New StructureMap.Configuration.DSL.Registry
            _Container = New StructureMap.Container(_Registry)
        End Sub
#End Region

    End Class
End Namespace```
Or something equally brilliant. You can now add things to the container even after calling resolve.

You can find more of this [Using the StructureMap Container independently of ObjectFactory by Jeremy][2] and [Comparing .NET DI (IoC) Frameworks, Part 2 by Andrey Shchekin][3]

 [1]: http://codebetter.com/blogs/jeremy.miller/
 [2]: http://codebetter.com/blogs/jeremy.miller/archive/2008/09/10/using-the-structuremap-container-independently-of-objectfactory.aspx
 [3]: http://blog.ashmind.com/index.php/2008/09/08/comparing-net-di-ioc-frameworks-part-2/