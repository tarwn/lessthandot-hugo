---
title: Exploring Reactive Extensions – Testing
author: Alex Ullrich
type: post
date: 2010-12-15T23:17:26+00:00
ID: 962
url: /index.php/desktopdev/mstech/exploring-reactive-extensions-testing/
views:
  - 8552
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies

---
Now that the [setup][1] is out of the way, let's look at how we can get this stuff tested. 

I'm a bit more interested in the observable side of the relationship so I'll start there, looking at the method using winforms events first. Testing our Send method when using events is just a matter of checking to see if the event we expected is raised. If we were doing this for real, we'd want some kind of mock handler that records not only whether it was called but what parameters were passed, etc... But for simplicity's sake we will just capture whether or not the handler got called.

```csharp
[Test]
public void Send() {
	var watched = new Watched();

	var onNextCalled = false;

	watched.OnNext += (sender, e) => onNextCalled = true;

	watched.Send("something");

	Assert.IsTrue(onNextCalled);
}
```

This isn't too bad thanks to the lambda expression, but it is important to note that we are only testing that the event is raised, not that the correct processing took place. On the other hand, look at the same test using the reactive approach.

```csharp
[Test]
public void Send() {
	var mockery = new MockRepository();
	var subscriber = mockery.StrictMock<IObserver<string>>();

	var message = "test message";

	var watched = new Watched();

	using (mockery.Record()) {
		subscriber.OnNext(message);
	}

	using (mockery.Playback())
	using (var unsubscriber = watched.Subscribe(subscriber)) {
		watched.Send(message);
	}
}
```

I get that the lambda in the event based method basically does the same thing as the mock subscriber we're using for the reactive method. But I feel like this approach models the actual behavior a bit better than the event driven approach did. I guess at the end of the day it is just a matter of preference. Hopefully I've made my preference clear 🙂

From the other side of the relationship, the testing approach is almost identical. Take a look at tests on the subscription for an example. Events first:

```csharp
[Test]
public void Subscribe_And_Unsubscribe_On_Complete() {
	var mockery = new MockRepository();
	var subject = mockery.StrictMock<IWatched<string>>();
	var unsubscriber = mockery.StrictMock<IDisposable>();

	var watcher = new Watcher<string>();

	using (mockery.Record()) {
		Expect.Call(subject.Subscribe(watcher)).Return(unsubscriber);
		unsubscriber.Dispose();
	}

	using (mockery.Playback()) {
		watcher.Subscribe(subject);
		watcher.OnCompleted(null, null);
	}
}
```

Not too complicated. We just want to make sure that subscribe works as expected, and that the subscription is cleaned up when OnCompleted is called. Now, to do the same thing with the reactive approach

```csharp
[Test]
public void Subscribe() {
	var mockery = new MockRepository();
	var subject = mockery.StrictMock<IObservable<string>>();
	var disposable = mockery.StrictMock<IDisposable>();

	var watcher = new Watcher();

	using (mockery.Record()) {
		Expect.Call(subject.Subscribe(watcher)).Return(disposable);
		disposable.Dispose();
	}

	using (mockery.Playback()){
		watcher.Subscribe(subject);
		watcher.OnCompleted();
	}
}
```

So the reactive approach doesn't change all that much on the observer side. But I think the observable side is the one we are more concerned about.

When dealing with the observables, I like how easy the reactive approach makes it to mock the other side of the relationship to confirm that the proper calls are being made on subscribers. This design seems to split the responsibility more evenly between the subscriber and the publisher, and this in turn makes the relationship a bit simpler to manage. The interface coupling that it introduces doesn't seem any worse to me than the implicit coupling you get when dealing with events. While the implications for testing in this scenario aren't exactly earth shattering, I do **like** testing the reactive approach better. The way the tests are structured just gives me a bit more confidence that things will go smoothly than the event based one.

_*****as usual, source code for this post can be found over at [github][2], with these examples in the TestingObservable project._

 [1]: /index.php/DesktopDev/MSTech/exploring-reactive-extensions-subscripti
 [2]: https://github.com/lessthandot/ExploringRx