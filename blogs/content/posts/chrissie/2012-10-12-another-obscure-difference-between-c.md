---
title: 'Another obscure difference between C# and VB.Net you probably don’t know about'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-12T04:51:00+00:00
ID: 1753
excerpt: 'Yes, there are more difference between C# and VB.Net than I care to remember and some have no reason to be there. This is one of the more obscure ones.'
url: /index.php/desktopdev/mstech/another-obscure-difference-between-c/
views:
  - 5807
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - difference
  - vb.net

---
I heard about this one on twitter from David Tchepak the author of [NSubstitute][1]. He referred to [a question][2] they got on the Google groups for NSubstitute.

The question is this.

> 1/ Create a new empty project (console application is ok)
  
> 2/ Add a single line in Main: Dim x As Castle.DynamicProxy.IProxyTargetAccessor
  
> Obviously this don&#8217;t compiles.
  
> 3/ Install NuGet package of Castle Windsor (3.1.0)
  
> Now, previous code line already compiles.
  
> 4/ Install NuGet package of NSubstitue (1.4.3.0)
  
> &#8216;IProxyTargetAccessor&#8217; is ambiguous in the namespace &#8216;Castle.DynamicProxy&#8217; error appears.

It is very easy to recreate this with the above steps.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nsub/nsub2.png?mtime=1350024037"><img alt="" src="/wp-content/uploads/users/chrissie1/nsub/nsub2.png?mtime=1350024037" width="546" height="154" /></a>
</div>

So what is the problem. 

NSubstitute has ILMerged a part of the Castle Windsor code into it&#8217;s own. And now it has part of that namespace too.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nsub/nsub3.png?mtime=1350024043"><img alt="" src="/wp-content/uploads/users/chrissie1/nsub/nsub3.png?mtime=1350024043" width="275" height="159" /></a>
</div>

This confuses the compiler. 

This is solvable in C#.

If you right-click on the NSubstitute reference you can change the alias of that reference (which is normally global).

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nsub/nsub1.png?mtime=1350023941"><img alt="" src="/wp-content/uploads/users/chrissie1/nsub/nsub1.png?mtime=1350023941" width="732" height="187" /></a>
</div>

And now the code will compile.

In VB.Net there is no such property to be found for a reference.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nsub/nsub4.png?mtime=1350024354"><img alt="" src="/wp-content/uploads/users/chrissie1/nsub/nsub4.png?mtime=1350024354" width="709" height="283" /></a>
</div>

And adapting the vbproj won&#8217;t help either, trust me, I tried 😉

In the csproj file you will see something like this.

```xml
&lt;ItemGroup&gt;
    &lt;Reference Include="Castle.Core"&gt;
      &lt;HintPath&gt;..packagesCastle.Core.3.1.0libnet35Castle.Core.dll&lt;/HintPath&gt;
    &lt;/Reference&gt;
    &lt;Reference Include="Castle.Windsor"&gt;
      &lt;HintPath&gt;..packagesCastle.Windsor.3.1.0libnet40Castle.Windsor.dll&lt;/HintPath&gt;
      &lt;Aliases&gt;castle1&lt;/Aliases&gt;
    &lt;/Reference&gt;
    &lt;Reference Include="NSubstitute"&gt;
      &lt;HintPath&gt;..packagesNSubstitute.1.4.3.0libNET40NSubstitute.dll&lt;/HintPath&gt;
      &lt;Aliases&gt;nsub&lt;/Aliases&gt;
    &lt;/Reference&gt;
    &lt;Reference Include="System" /&gt;
    &lt;Reference Include="System.Core" /&gt;
    &lt;Reference Include="System.Xml.Linq" /&gt;
    &lt;Reference Include="System.Data.DataSetExtensions" /&gt;
    &lt;Reference Include="Microsoft.CSharp" /&gt;
    &lt;Reference Include="System.Data" /&gt;
    &lt;Reference Include="System.Xml" /&gt;
  &lt;/ItemGroup&gt;```
See the Aliases tag on some of the references?

Lets copy that to the vbproj file and compile again.

```xml
&lt;ItemGroup&gt;
    &lt;Reference Include="Castle.Core"&gt;
      &lt;HintPath&gt;..packagesCastle.Core.3.1.0libnet35Castle.Core.dll&lt;/HintPath&gt;
    &lt;/Reference&gt;
    &lt;Reference Include="Castle.Windsor"&gt;
      &lt;HintPath&gt;..packagesCastle.Windsor.3.1.0libnet40Castle.Windsor.dll&lt;/HintPath&gt;
    &lt;/Reference&gt;
    &lt;Reference Include="NSubstitute"&gt;
      &lt;HintPath&gt;..packagesNSubstitute.1.4.3.0libNET40NSubstitute.dll&lt;/HintPath&gt;
      &lt;Aliases&gt;nsub&lt;/Aliases&gt;
    &lt;/Reference&gt;
    &lt;Reference Include="System" /&gt;
    &lt;Reference Include="System.Data" /&gt;
    &lt;Reference Include="System.Deployment" /&gt;
    &lt;Reference Include="System.Xml" /&gt;
    &lt;Reference Include="System.Core" /&gt;
    &lt;Reference Include="System.Xml.Linq" /&gt;
    &lt;Reference Include="System.Data.DataSetExtensions" /&gt;
  &lt;/ItemGroup&gt;```
But no, it still won&#8217;t compile.

Maybe this is something for VS2020. Who knows.

 [1]: http://nsubstitute.github.com/
 [2]: https://groups.google.com/forum/?fromgroups=#!topic/nsubstitute/IQjTIDn-GkA