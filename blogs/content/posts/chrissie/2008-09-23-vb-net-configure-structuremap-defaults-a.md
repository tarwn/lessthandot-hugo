---
title: 'VB.Net: Configure StructureMap defaults and non-defaults'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-09-23T09:36:16+00:00
ID: 150
url: /index.php/desktopdev/mstech/vb-net-configure-structuremap-defaults-a/
views:
  - 34138
categories:
  - Microsoft Technologies

---
By now you should already know how to add a default implementation into StructureMap but would it not be nice that you also had a nice way to add a non-default/second implemntation to the configuration? Let me answer that question for you, Yes it would.

So what am I waiting for? I&#8217;m waiting for you to download the source from sourceforge (https://structuremap.svn.sourceforge.net/svnroot/structuremap/trunk/Source/StructureMap). And then you compile it and use the StructureMap from that build. And now we continue with our regular program.

First this is one method of adding a default implementation to StructureMap.

```vbnet
Dim _Registry as new tructureMap.Configuration.DSL.Registry 
_Registry.ForRequestedType(Of ITest)().TheDefault.Is.OfConcreteType(Of Test1).WithName("name1")```
This works fine and well if we do this

```vbnet
Dim _Container as New StructureMap.Container(_Registry)
_Container.GetInstance(Of ITest)()```
this will bring back the implementation of Test1.
  
And so will this BTW

```vbnet
_Container.GetInstance(Of ITest)("name1")```
But now I have a second implementation of ITest namely Test2 (who said I wasn&#8217;t original?). Test1 is still the default implementation but sometimes we need to use Test2 as the implementation. So we do this.

```vbnet
_Registry.ForRequestedType(Of ITest).AddInstances(Function(e) e.OfConcreteType(Of Test2).WithName("name2"))```
Or this.

```vbnet
_Registry.InstanceOf(Of ITest).Is.OfConcreteType(Of Test2).WithName("name2")```
Both give the same results. Now if I do this.

```vbnet
Dim _Container as New StructureMap.Container(_Registry)
_Container.GetInstance(Of ITest)()```
I will get the implemntation of Test1.

But if I want Implemntation number 2. I do this.

```vbnet
_Container.GetInstance(Of ITest)("name2")```
So it is quite simple if someone tells you 😉