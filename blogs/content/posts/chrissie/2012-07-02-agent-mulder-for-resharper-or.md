---
title: Agent mulder for resharper or how to see if a type is registered by your DI container
author: Christiaan Baes (chrissie1)
type: post
date: 2012-07-02T05:47:00+00:00
ID: 1663
excerpt: Agent Mulder plugin for ReSharper analyzes DI containers (Dependency Injection, sometimes called Inversion of Control, or IoC containers) in your solution, and provides navigation to and finding usages of types registered or resolved by those containers.
url: /index.php/desktopdev/mstech/agent-mulder-for-resharper-or/
views:
  - 7991
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - agent mulder
  - resharper

---
## Introduction

[Agent Mulder][1] is a plugin for [resharper][2], so you will need that.

> Agent Mulder plugin for ReSharper analyzes DI containers (Dependency Injection, sometimes called Inversion of Control, or IoC containers) in your solution, and provides navigation to and finding usages of types registered or resolved by those containers.

You will clearly also need a DI container 😉

I will test it with Autofac and VB.Net (of course) and I used Agent mulder 1.0.4 for this.

## Installation

But first you install it. [Download the msi][3] first or [the source][4] and compile it yourself.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mulder/Mulder1.png?mtime=1341212474"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mulder/Mulder1.png?mtime=1341212474" width="870" height="670" /></a>
</div>

When installed, you should see this in the Resharper -> Options -> plugins window thing.

## The code

Let me first show you this with some C#.

Here is my test code.

```csharp
using Autofac;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            ContainerBuilder builder;
            builder = new ContainerBuilder();
            builder.RegisterType&lt;Service1&gt;().As&lt;IService1&gt;();
            var container = builder.Build();
        }
    }

    internal class Class1
    {
        private IService1 _service1;
        
        public Class1(Service1 service1)
        {
            _service1 =service1;
        }

    }

    interface IService1
    {}

    class Service1: IService1
    {
    }

}
```
If you have the resharper solution wide analysis turned on you will see this on the Service1 class.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mulder/Mulder2.png?mtime=1341214353"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mulder/Mulder2.png?mtime=1341214353" width="281" height="81" /></a>
</div>

This is because we inject our service in to our class and never instantiate it anywhere. We let our DI-container deal with that. So resharper will always give this error.

Now if we use the Agent mulder plugin this warningwill go away.

But now we will see an icon appear.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mulder/Mulder3.png?mtime=1341214738"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mulder/Mulder3.png?mtime=1341214738" width="396" height="81" /></a>
</div>

Which tells us agent mulder has detected it&#8217;s existence and knows it has been configured in our DI-container.

IF you now comment out the line 

```csharp
builder.RegisterType&lt;Service1&gt;().As&lt;IService1&gt;();```
It will give the warning again.

What is even more amazing is that you can click on the icon and navigate to where service has been registered.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mulder/Mulder4.png?mtime=1341215109"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/mulder/Mulder4.png?mtime=1341215109" width="236" height="91" /></a>
</div>

Sadly Agent mulder doesn&#8217;t seem to work in VB.Net.

## Conclusion

Agent mulder is a great plugin if you work with DI containers an resharper.

 [1]: http://hmemcpy.github.com/AgentMulder/
 [2]: http://www.jetbrains.com/resharper/
 [3]: https://github.com/downloads/hmemcpy/AgentMulder/AgentMulder1.0.4.msi
 [4]: https://github.com/hmemcpy/AgentMulder