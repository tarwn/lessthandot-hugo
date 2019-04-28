---
title: Raising an event on a parametered event with Rhino mocks and returning null
author: Christiaan Baes (chrissie1)
type: post
date: 2010-02-15T06:57:46+00:00
ID: 702
url: /index.php/desktopdev/mstech/csharp/raising-an-event-on-a-parmeterd-event-wi/
views:
  - 4080
categories:
  - 'C#'
tags:
  - raise event
  - rhino mocks

---
Raising events on a mock is easy with Rhino mocks. [I wrote a whole article about it][1]. 

Way back then I wrote,

> If we try to use the same syntax on the parameterless event like so eventraiser.GetEventRaiser(e => e.event1 += null).Raise(null); we get an InvalidOperationException (You have called the event raiser with the wrong number of parameters. Expected 0 but was 0). Expected 0 but was 0 doesn&#8217;t sound completely right but I get the picture. If you try to raise an event on a parameterd event like you would on a parameterless event, like so eventraiser.Raise(e => e.event2 += null); then you get this InvalidOperationException (You have called the event raiser with the wrong number of parameters. Expected 1 but was 0) where the 1 is the number of parameters you should have.

And I forgot that sometimes you do want to pass null as an argument to check if that raises and exception or to check if your code handles that situation like you intended.

So I have some code that raises an event that takes an object as a parameter. Something like this.

```csharp
using System;
using Rhino.Mocks;
using NUnit.Framework;
 
namespace TestingRhinoMocks
{
    public delegate void MyDelegate(Object test);
    
    public interface IEventsRaiser
    {
        event MyDelegate event;
    }
 
    public class EventRaiser : IEventsRaiser
    {
        public event MyDelegate event;
    }
 
    public class EventConsumer
    {
        private IEventsRaiser eventraiser;
 
        public EventConsumer(IEventsRaiser eventraiser)
        {
            this.eventraiser = eventraiser;
            eventraiser.event1 += Eventhandler1;
        }
 
        public Object Message { get; set; }
 
        public void Eventhandler(Object test)
        {
            Message = test;
        }
   
    }
```
So when I do this.

```csharp
/// &lt;summary&gt;
        /// RaiseEventWithNullPassedIn
        /// &lt;/summary&gt;
        [Test]
        public void RaiseEventWithNullPassedIn()
        {
            var mocks = new StructureMap.AutoMocking.RhinoAutoMocker&lt;EventConsumer&gt;();
            var e1 = mocks.ClassUnderTest;
            var e2 = mocks.Get&lt;IEventsRaiser &gt;();
            facade.GetEventRaiser(x =&gt; x.event += null).Raise(null);
            Assert.IsNull(e1.Message);
        }

```
I will get the exception.

But when I do this the test passes.

```csharp
/// &lt;summary&gt;
        /// RaiseEventWithNullPassedIn
        /// &lt;/summary&gt;
        [Test]
        public void RaiseEventWithNullPassedIn()
        {
            var mocks = new StructureMap.AutoMocking.RhinoAutoMocker&lt;EventConsumer&gt;();
            var e1 = mocks.ClassUnderTest;
            var e2 = mocks.Get&lt;IEventsRaiser &gt;();
            Object o = null;
            facade.GetEventRaiser(x =&gt; x.event += null).Raise(o);
            Assert.IsNull(e1.Message);
        }

```
Did you see the subtle difference? Very subtle indeed.

 [1]: http://www.google.be/url?sa=t&source=web&ct=res&cd=5&ved=0CCMQFjAE&url=http%3A%2F%2Fblogs.ltd.local%2Findex.php%2FDesktopDev%2FMSTech%2Frhino-mocks-and-raising-an-event-that-ha&ei=fgx5S_3ALcr04gaD362yCg&usg=AFQjCNEOHxpmJzYOCPlgeUmDi_Sb-TuHOA&sig2=sg_z21MPF4xC2OwZFFc9oA