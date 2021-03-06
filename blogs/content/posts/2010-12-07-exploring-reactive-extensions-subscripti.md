---
title: Exploring Reactive Extensions – Subscription Management
author: Alex Ullrich
type: post
date: 2010-12-07T23:39:00+00:00
ID: 965
url: /index.php/desktopdev/mstech/exploring-reactive-extensions-subscripti/
views:
  - 5690
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - 'reactiveextensions c#'

---
I sat down to write a post on testing the core interfaces in the reactive library, and found myself going off onto a bit of a tangent. I made a big enough change to the way everything was set up that I didn't really feel comfortable including it in the post on testing, but it seemed worth mentioning. So this post was born. 

I started thinking about an object responsible for managing the subscription, which can be returned as the IDisposable from the observable's Subscribe method. I think this could help not only with usability, but testability as well.

Following the reactive model, it basically holds a reference to the observable's list of subscribers and observer involved in the subscription, and takes care of removing the observer from the list when disposed. What's nice about this is that it allows you to subscribe from the observer like this:

```csharp
IDisposable subscription;
public void Subscribe(IObservable<string> subject) {
	subscription = subject.Subscribe(this);
}
```

And the subscribe method looks like this:

```csharp
public IDisposable Subscribe(IObserver<string> watcher) {
	watchers.Add(watcher);
	return new Subscription<string>(watchers, watcher);
}
```

Then, by disposing of the unsubscriber the observer can politely remove itself from the list of objects to notify. The unsubscriber class is pretty simple, and the way it's being used no one ever needs to know its something besides an IDisposable. 

```csharp
public class Subscription<T> : IDisposable {
	IList<IObserver<T>> observers;
	IObserver<T> observer;

	public Subscription(IList<IObserver<T>> observers, IObserver<T> observer) {
		this.observers = observers;
		this.observer = observer;
	}

	public void Dispose() {
		if (observer != null && observers.Contains(observer))
			observers.Remove(observer);
	}
}
```

This was really the only significant code change to the reactive way of doing things discussed in the first post. Doing it with events is not terribly difficult, but it is different. The observable interface is replaced by an interface exposing three events:

```csharp
public interface IWatched<T> {
	event EventHandler<DataEventArgs<T>> OnNext;
	event EventHandler<DataEventArgs<Exception>> OnError;
	event EventHandler OnCompleted;
	IDisposable Subscribe(IWatcher<T> watcher);
}
```

Dealing with traditional events, much more of the responsibility is left to the observer. Using IObservable, it becomes the event source's responsibility to call the appropriate methods on the subscribing objects. Using events, the publisher's only responsibility is to raise events with the appropriate data for consumption by any interested subscribers. This feels like a much more passive approach, at least from the publisher's end. I know the difference isn't huge, but when it comes to testing it **feels**, at least conceptually, like there is a large gap between publishers and consumers of notifications because the winforms event system sits between them. 

I also think the need to send everything as EventArgs makes things a bit tougher to read, but not overly so. It does seem like it adds unnecessary overhead to the task at hand though. The fact that it allows you to pass normal objects is certainly an advantage of the reactive method. 

Following the non-reactive approach, the Subscription object has a bit more to it. Using this kind of interface for the subject of observation, subscribing to updates becomes an exercise in wiring up event handlers. There is not as much to what the event source is doing using this approach, so I'm not sure how much sense it makes to store a collection of subscribers on the observable. But we can still keep a subscribe method there. In this case, the concept of the Unsubscriber made sense as a place to do not only the unsubscribing but also the initial subscription for all events. It's probably due for a rename then. This new Subscription class will look like this:

```csharp
public class Subscription<T> : IDisposable {
	IWatched<T> subject;
	EventHandler<DataEventArgs<T>> nextHandler;		
	EventHandler<DataEventArgs<Exception>> errorHandler;
	EventHandler completeHandler;

	public Subscription(IWatched<T> toWatch, 
		IWatcher<T> watcher) {

		this.nextHandler = new EventHandler<DataEventArgs<string>>(watcher.OnNext);
		this.errorHandler = new EventHandler<DataEventArgs<Exception>>(watcher.OnError);
		this.completeHandler = new EventHandler(watcher.OnCompleted);

		subject = toWatch;

		subject.OnNext += nextHandler;
		subject.OnError += errorHandler;
		subject.OnCompleted += completeHandler;
	}

	public void Dispose() {
		subject.OnNext -= nextHandler;
		subject.OnError -= errorHandler;
		subject.OnCompleted -= completeHandler;
		subject = null;
	}
}
```

I think it's nice to have this stuff in a self contained object, and it certainly helps us to keep testing approaches somewhat similar. A nice benefit of this work is that if we have pieces of the subscribe method implemented on both sides it opens up some nice options for testing. And we can discuss them when I get to the real post 🙂

_*****Source code for this post can be found over at [github][1], with these examples in the TestingObservable project._

 [1]: https://github.com/lessthandot/ExploringRx