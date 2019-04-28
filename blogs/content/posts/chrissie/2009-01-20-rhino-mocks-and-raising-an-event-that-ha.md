---
title: Rhino mocks and raising an event that has parameters and the AAA syntax
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-20T07:26:10+00:00
ID: 290
url: /index.php/desktopdev/mstech/rhino-mocks-and-raising-an-event-that-ha/
views:
  - 12738
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - aaa syntax
  - 'c#'
  - rhino mocks

---
Testing things with Rhino mocks and the AAA syntax is simple. But sometimes I need a reminder of the correct syntax. And sometimes the result is not what I would expect. But then that&#8217;s what my users say about my programs too ;-). 

I wrote a **little** testcase to see if I could reproduce the same result. And like I said before Rhino moccks&#8217; AAA syntax is much easier to use in C# but I will make a VB.Net version for this too. So don&#8217;t worry. 

The problem is understanding the difference between raising an event that has a delegate without parameters and raising an event that has parameters. Let me show you the code first.

```csharp
using System;
using Rhino.Mocks;
using NUnit.Framework;

namespace TestingRhinoMocks
{
    public delegate void MyDelegate1();
    public delegate void MyDelegate2(string test);
    public delegate void MyDelegate3(string test1, int test2);
    public delegate void MyDelegate4(object test);
    public delegate void MyDelegate5(object test1, object test2);
    public delegate void MyDelegate6(object test1, object test2, Object test3);

    public interface IEventsRaiser
    {
        event MyDelegate1 event1;
        event MyDelegate2 event2;
        event MyDelegate3 event3;
        event MyDelegate4 event4;
        event MyDelegate5 event5;
        event MyDelegate6 event6;
    }

    public class EventRaiser : IEventsRaiser
    {
        public event MyDelegate1 event1;
        public event MyDelegate2 event2;
        public event MyDelegate3 event3;
        public event MyDelegate4 event4;
        public event MyDelegate5 event5;
        public event MyDelegate6 event6;
    }

    public class EventConsumer
    {
        private IEventsRaiser eventraiser;

        public EventConsumer(IEventsRaiser eventraiser)
        {
            this.eventraiser = eventraiser;
            eventraiser.event1 += Eventhandler1;
            eventraiser.event2 += Eventhandler2;
            eventraiser.event3 += Eventhandler3;
            eventraiser.event4 += Eventhandler4;
            eventraiser.event5 += Eventhandler5;
            eventraiser.event6 += Eventhandler6;
        }

        public string Message { get; set; }

        public void Eventhandler1()
        {
            Message = "Eventhandler1";
        }
        public void Eventhandler2(string test)
        {
            Message = "Eventhandler2";
        }
        public void Eventhandler3(string test1, int test2)
        {
            Message = "Eventhandler3";
        }
        public void Eventhandler4(object test)
        {
            Message = "Eventhandler4";
        }
        public void Eventhandler5(object test1, object test2)
        {
            Message = "Eventhandler5";
        }
        public void Eventhandler6(object test1, object test2, Object test3)
        {
            Message = "Eventhandler6";
        }

    }

    [TestFixture]
    public class TestRaiseEvents 
    {
        private IEventsRaiser eventraiser;
        private EventConsumer eventsConsumer;

        [SetUp]
        public void SetUp()
        {
            eventraiser = MockRepository.GenerateMock&lt;IEventsRaiser&gt;();
            eventsConsumer = new EventConsumer(eventraiser);
        }

        [Test]
        public void Test_raising_event1_without_parameters_using_raise()
        {
            Assert.IsNull(eventsConsumer.Message);
            eventraiser.Raise(e =&gt; e.event1 += null);
            Assert.AreEqual("Eventhandler1", eventsConsumer.Message);
        }

        [Test]
        [ExpectedException(ExceptionName = "System.InvalidOperationException", ExpectedMessage = "You have called the event raiser with the wrong number of parameters. Expected 0 but was 0")]
        public void Test_raising_event1_without_parameters_using_geteventraiser_and_raise()
        {
            Assert.IsNull(eventsConsumer.Message);
            eventraiser.GetEventRaiser(e =&gt; e.event1 += null).Raise(null);
            Assert.AreEqual("Eventhandler1", eventsConsumer.Message);
        }

        [Test]
        [ExpectedException(ExceptionName = "System.InvalidOperationException", ExpectedMessage = "You have called the event raiser with the wrong number of parameters. Expected 1 but was 0")]
        public void Test_raising_event2_with_parameters_wrongly_called()
        {
            Assert.IsNull(eventsConsumer.Message);
            eventraiser.Raise(e =&gt; e.event2 += null);
            Assert.AreEqual("Eventhandler2", eventsConsumer.Message);
        }

        [Test]
        public void Test_raising_event2_with_parameters()
        {
            Assert.IsNull(eventsConsumer.Message);
            eventraiser.GetEventRaiser(e =&gt; e.event2 += null).Raise("test");
            Assert.AreEqual("Eventhandler2", eventsConsumer.Message);
        }

        [Test]
        public void Test_raising_event3_with_parameters()
        {
            Assert.IsNull(eventsConsumer.Message);
            eventraiser.GetEventRaiser(e =&gt; e.event3 += null).Raise("test", 0);
            Assert.AreEqual("Eventhandler3", eventsConsumer.Message);
        }

        [Test]
        public void Test_raising_event4_with_parameters()
        {
            Assert.IsNull(eventsConsumer.Message);
            eventraiser.GetEventRaiser(e =&gt; e.event4 += null).Raise(new Object());
            Assert.AreEqual("Eventhandler4", eventsConsumer.Message);
        }

        [Test]
        public void Test_raising_event5_with_parameters()
        {
            Assert.IsNull(eventsConsumer.Message);
            eventraiser.GetEventRaiser(e =&gt; e.event5 += null).Raise(new Object(), new Object());
            Assert.AreEqual("Eventhandler5", eventsConsumer.Message);
        }

        [Test]
        public void Test_raising_event6_with_parameters()
        {
            Assert.IsNull(eventsConsumer.Message);
            eventraiser.GetEventRaiser(e =&gt; e.event6 += null).Raise(new Object(), new Object(), new Object());
            Assert.AreEqual("Eventhandler6", eventsConsumer.Message);
        }
    }
}```
As always the code speaks for itself ;-). But I&#8217;ll try to explain it anyway. I have a class that raisesevents and an interface for that class so I can mock it easily. Then I have a class that consumes the events. I used 6 different events with different signatures just to prove that it works like I think it should. 

So Test number 1 has the correct syntax to raise an event that takes no parameters. Namely **eventraiser.Raise(e => e.event1 += null);** We use the extension method Raise to raise the event on our mock. Now this is different from raising an event that has parameters in the way that **eventraiser.GetEventRaiser(e => e.event2 += null).Raise(&#8220;test&#8221;);** uses GetEventRaiser and then the method Raise to add the parameter. If we try to use the same syntax on the parameterless event like so **eventraiser.GetEventRaiser(e => e.event1 += null).Raise(null);** we get an InvalidOperationException (You have called the event raiser with the wrong number of parameters. Expected 0 but was 0). Expected 0 but was 0 doesn&#8217;t sound completely right but I get the picture. If you try to raise an event on a parameterd event like you would on a parameterless event, like so **eventraiser.Raise(e => e.event2 += null);** then you get this InvalidOperationException (You have called the event raiser with the wrong number of parameters. Expected 1 but was 0) where the 1 is the number of parameters you should have. 

I hope everything is clear now on how to raise an event on a mock object. 

I used Rhino mocks 3.5.0.1337 for this. 

Comments welcome.