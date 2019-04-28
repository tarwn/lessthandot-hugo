---
title: Nancy and the case of the camel
author: Christiaan Baes (chrissie1)
type: post
date: 2014-03-20T08:31:30+00:00
ID: 2534
url: /index.php/uncategorized/nancy-and-the-case-of-the-camel/
views:
  - 6841
categories:
  - Uncategorized

---
Did you know that there was a breaking change in Nancy 0.22? Did you check before updating that nuget package? If you answered no to the previous questions than you are probably me and did a booboo in production where you freaked someone else out, despite it taking them a few days to figure out it was no longer working. My code wasn&#8217;t phased by the change probably because VB.Net is superior and they use C# (le sigh). 

Anyway, the json produced by Nancy 0.22 and up is now camelCased and no longer PascalCased. As can be read in <a href="https://github.com/NancyFx/Nancy/issues/1362" title="Github issue 1362" target="_blank">this issue on Github</a>.

So how do I now return it so it outputs PascalCasing again. 

That&#8217;s simple (once you know how).

In your custom bootstrapper, which you just add somewhere in your project and Nancy will find it and lovingly use it. 

you put this.<pre lang=vbnet> Public Class BootStrapper Inherits DefaultNancyBootstrapper Protected Overrides Sub ApplicationStartup(container As TinyIoc.TinyIoCContainer, pipelines As IPipelines) MyBase.ApplicationStartup(container, pipelines) Json.JsonSettings.RetainCasing = True End Sub End Class </pre> 

It&#8217;s the Json.JsonSettings.RetainCasing that does the trick. True means I want PascalCasing, False means bad things happen to the other peoples code.

And that&#8217;s a shared property. 

And share properties are hard to test.

But I tested it anyway, because I&#8217;m slightly awesome.

First I needed to override my bootstrapper (you might not need to do that, but I have several testbootstrappers in my testproject already and nancy picks those up and becomes sad).<pre lang=vbnet> Private Class OverridenBootStrapper Inherits BootStrapper Protected Overrides ReadOnly Property RootPathProvider As IRootPathProvider Get Return New DefaultRootPathProvider End Get End Property End Class </pre> 

Where it inherits from the above bootstrapper I showed you.

And now the test.<pre lang=vbnet> <Test> Public Sub BootstrapperShouldSetRetainCasing() Json.JsonSettings.RetainCasing = False Assert.IsFalse(Json.JsonSettings.RetainCasing) Dim browser = New Browser(_bootstrapper) Assert.IsTrue(Json.JsonSettings.RetainCasing) End Sub </pre> 

It&#8217;s a shared property remember so we have to do interesting things to it. 

And nCrunch now says it loves me. I&#8217;m sure green means it loves me. 

And tada everyone is happy again.

Did you like the title, I thought it was very clever of me.