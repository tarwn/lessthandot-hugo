---
title: Exploring Reactive Extensions – IObservable and IObserver
author: Alex Ullrich
type: post
date: 2010-11-20T22:10:00+00:00
url: /index.php/desktopdev/mstech/exploring-reactive-extensions-iobservabl/
views:
  - 23713
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'reactiveextensions linq c#'

---
A while ago, a coworker ([Jon][1]) showed us a presentation over lunch that he had given at the local alt.net user group on the [Reactive Extensions for .NET][2]. I was pretty unfamiliar with the subject, but once I got the lowdown from this presentation it was quite clear that this was something I&#8217;d need to explore. At the most basic level, the reactive extensions are about setting up your objects to _react_ to something they are subscribed to. In the presentation, he used an analogy of a reversed, or push-based IEnumerable, and I like that a lot. There is a ton of cool stuff in this library that I hope to get into later, but it all hinges on that idea.

I&#8217;ve only recently been getting back into windows desktop development (coming from a more web-centric environment) so it is possible that I&#8217;m overreacting to the pain of dealing with events in windows forms, but this paradigm really appeals to me. It gives you a way to call methods on your observers _from the object they are observing_, through a standard set of interfaces. The simpler parts are possible using the normal event mechanisms found in .net, but keeping the interaction limited to a standard set of interfaces (and explicitly calling the code from a central location) feels much simpler to me. 

## Interface Overview

The two interfaces we need are remarkably simple, but very powerful. The generic IObservable interface only has one method, Subscribe, taking an IObserver of the same type. This allows the observers to register with the object they need to observe, in order to receive updates. The IObserver interface contains three methods, OnNext(T), OnError and OnComplete. I think these are all pretty straightforward. OnNext and OnComplete allow you to process items and figure out when processing is complete, making those two where most of the magic happens. OnError may be less sexy, but it may be the most useful of the bunch, especially when dealing with external resources that may not always be available.

## A Quick Example

I really struggled to think of a simple, &#8220;hello world&#8221; type application for this. In the presentation I saw, the sample application was a tool to query netflix&#8217;s oData service. This was cool (especially the auto-complete) but seems like a bit much for a quick introduction, and I don&#8217;t want to steal Jon&#8217;s idea. Sometimes I wish I had one of those clocks that show several time zones at once, like the ones they use in call centers, so I could check before bothering some of my friends from lessthandot at unholy hours. I don&#8217;t have nearly enough interest in figuring out every country&#8217;s wacky daylight savings time rules to actually make such a thing, but decided it could be a decent example. It consists of several elements getting data from a single source, and doing some of their own processing on the data before passing it along, which is exactly what I wanted to show. So we&#8217;ll work with the clockmaster 3000.

We&#8217;ll start with the observable. In this case it is just a class that sends time updates to any subscribers. I called it the Atomic Clock because it sounds cool. The Subscribe method is the most important here.

<pre>class AtomicClock : IObservable&lt;DateTime&gt; {
	IList&lt;IObserver&lt;DateTime&gt;&gt; observers = new List&lt;IObserver&lt;DateTime&gt;&gt;();
	bool keepRunning;

	public IDisposable Subscribe(IObserver&lt;DateTime&gt; observer) {
		observers.Add(observer);
		return observer as IDisposable;
	}

	public void Complete() {
		keepRunning = false;
		foreach (var observer in observers) {
			observer.OnCompleted();
		}
	}

	public void Run() {
		keepRunning = true;
		while (observers.Count &gt; 0 && keepRunning) {
			SendTime();
		}
	}

	void SendTime() {
		foreach (var observer in observers) {
			observer.OnNext(DateTime.UtcNow);
		}
		System.Threading.Thread.Sleep(1000);
	}
}</pre>

The most fun we&#8217;re having here is probably SendTime method. It is not that interesting, but it&#8217;s easy to see this being any kind of push-based notification. I really like the shift of control from the observer (where it would be using traditional event mechanisms) to the observed object that this represents. The observer is still in control of _what_ to do with the information received, but the observed is in (more explicit) control of _when_ it does it. If you&#8217;re using .net 4 (I don&#8217;t have it on my laptop) this could be sped up by using Parallel.ForEach (or use something like [this][3] on 3.5) but that&#8217;s not too important at this point. Note that the Subscribe method returns an IDisposable &#8211; I&#8217;m not actually using it in this example but it would be very convenient when the observer needs to deal with unmanaged resources. The only other thing worth noting is the keepRunning boolean &#8211; this is used to signal to the clock that it can stop running it&#8217;s running on a separate thread.

Now to set up the observers. In this case, I&#8217;m just implementing the IObserver interface on a winforms control that contains a couple of labels (city name and time). They also take an offset property, which is used to localize the UTC time received from the atomic clock (as long as we are willing to pretend there is no such thing as daylight savings). There is some cruft in there to handle crossthread calls on controls, but it could be worse.

<pre>public partial class Clock : UserControl, IObserver&lt;DateTime&gt; {
	public Clock() {
		InitializeComponent();
	}

	public string City {
		set { clockName.Text = value; }
		get { return clockName.Text; }
	}

	public double Offset { get; set; }

	public void OnCompleted() {
		SetTimeText(time, "Not Responding");
	}

	public void OnNext(DateTime next) {
		string current = next.AddHours(Offset).ToLongTimeString();
		SetTimeText(time, current);
	}

	public void OnError(Exception ex) {
		SetTimeText(time, "Error reaching time service");
	}

	Action&lt;Label, string&gt; setterCallback = (toSet, text) =&gt; toSet.Text = text;

	void SetTimeText(Label toSet, string text) {
		if (time.InvokeRequired) {
			this.Invoke(setterCallback, toSet, text);
		}
		else {
			setterCallback(toSet, text);
		}
	}
}</pre>

We can throw a few of these on a form, set up the correct city names and offsets, and then its time to wire everything up. The only buttons we&#8217;ve got are to start and stop the clock. Here&#8217;s the code for the form:

<pre>public partial class GlobalClock : Form {
	AtomicClock atomicClock = new AtomicClock();
	System.Threading.Thread backgroundThread;
	bool running;

	public GlobalClock() {
		InitializeComponent();
		foreach (var control in this.Controls) {
			if (control.GetType() == typeof(MasterClock.Clock)) {
				atomicClock.Subscribe(control as IObserver&lt;DateTime&gt;);
			}
		}
		this.FormClosing += OnClosing;
	}

	private void Run_Click(object sender, EventArgs e) {
		if (!running) {
			backgroundThread = new Thread(atomicClock.Run);
			backgroundThread.Start();
			running = true;
		}
	}

	private void Stop_Click(object sender, EventArgs e) {
		StopService();
	}

	void OnClosing(Object sender, FormClosingEventArgs e) {
		StopService();
	}

	void StopService() {
		atomicClock.Complete();
		running = false;
	}
}</pre>

Hopefully this provided a decent introduction. A VS2008 solution containing all the code can be found over at [github][4], and any further projects will be added there (this post focused on the ClockMaster3000 project).

It appears that the reactive extensions can be tricked into [working with mono][5] (binaries can be found in an interesting location, **C:Program FilesMicrosoft Cloud ProgrammabilityReactive Extensions** if you need to copy them over). I had some trouble with this at first because of lingering GAC references in the project but it does appear that when referenced locally the reactive framework &#8220;just works&#8221;. I&#8217;ll update the project on github to include local references for any other mono folks.

 [1]: http://twitter.com/jon_graves
 [2]: http://msdn.microsoft.com/en-us/devlabs/ee794896.aspx
 [3]: /index.php/WebDev/ServerProgramming/a-simple-parallel-foreach-implementation-5
 [4]: https://github.com/lessthandot/ExploringRx
 [5]: http://stackoverflow.com/questions/2677755/is-the-reactive-framework-rx-available-for-use-in-mono-yet