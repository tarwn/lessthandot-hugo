---
title: Azure Worker Role – Exiting Safely
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-02-11T18:59:00+00:00
excerpt: |
  The basic Azure Worker Role consists of a run method, an endless loop, and a sleep statement. Earlier this week, Magnus Martensson walked through implementing a more sophisticated wait object than the generic Thread.Sleep call. Which reminded me of a problem inherent in the basic Microsoft template.
  
  Every exit is a crash.
url: /index.php/desktopdev/mstech/azure-worker-role-exiting-safely/
views:
  - 18730
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft Technologies
tags:
  - azure
  - worker role

---
The basic Azure Worker Role consists of a run method, an endless loop, and a sleep statement. Earlier this week, Magnus Martensson walked through [implementing a more sophisticated wait object][1] than the generic Thread.Sleep call. Which reminded me of a problem inherent in the basic Microsoft template.

Every exit is a crash.

The basic Worker Role is a while(true) statement that alternates between doing work and sleeping for a period of time. When it&#8217;s time for Azure to recycle the instance, deploy a new one, scale&#8230;what happens to this while(true) statement?

It&#8217;s killed. 

The more critical it was, the higher our chances it was in the work side of the work/sleep loop.

(Ouch.)

## The Basic Worker Role

Here is the worker class that Visual Studio generates on when creating a new Worker Role project:

<pre>public class WorkerRole : RoleEntryPoint
{
	public override void Run()
	{
		// This is a sample worker implementation. Replace with your logic.
		Trace.WriteLine("WorkerRole1 entry point called", "Information");

		while (true)
		{
			Thread.Sleep(10000);
			Trace.WriteLine("Working", "Information");
		}
	}

	public override bool OnStart()
	{
		// Set the maximum number of concurrent connections 
		ServicePointManager.DefaultConnectionLimit = 12;

		// For information on handling configuration changes
		// see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

		return base.OnStart();
	}
}</pre>

Azure calls the OnStart when it starts, then calls the Run method. This sample will hard crash when Azure scales it out of existence, swaps in new instances, decides it&#8217;s Windows patch time, lets us press the Stop button, and so on. 

Let&#8217;s see it in action. I&#8217;ve added a DoWork() method that sleeps for 10 seconds to simulate important work being done. I&#8217;ve also added Trace.WriteLine calls to the existing methods and to an override of the OnStop method, so we can see what&#8217;s happening.

<pre>public override void Run()
{
	// This is a sample worker implementation. Replace with your logic.
	Trace.WriteLine("BasicWorker - Entry point called", "Information");

	while (true)
	{
		Thread.Sleep(10000);
		Trace.WriteLine("BasicWorker - Starting some work", "Information");
		DoWork();
		Trace.WriteLine("BasicWorker - Finished some work", "Information");
	}
}

public void DoWork()
{
	Thread.Sleep(10000);
}

public override void OnStop()
{
	Trace.WriteLine("BasicWorker - OnStop", "Information");
}</pre>

If we run this in the emulator and suspend the worker role in the middle of our important work, it exits right on cue, in the middle of the work.

**Diagnostic Trace Output**

<pre>Information: 000.1s - BasicWorker - Entry point called
Information: 010.1s - BasicWorker - Starting some work
Information: 020.1s - BasicWorker - Finished some work
Information: 030.1s - BasicWorker - Starting some work
Information: 040.1s - BasicWorker - Finished some work
Information: 050.1s - BasicWorker - Starting some work
Information: 056.1s - BasicWorker - OnStop</pre>

_note: I later added timestamps to the Trace output for readability_

If this were a real worker role, we could have been doing just about anything in that step when it was killed. Is our system still in a good state?

## A Cancel-able Worker Role

The base class for a WorkerRole is the RoleEntryPoint. As we saw above, it offers an OnStop method that will be called when the instance is suspended. More importantly, though, we are allowed to delay that OnStop method [up to 30 seconds][2] to finish up what we are working on.

_Note: Early last year (2012) this was extended to 5 minutes, though it&#8217;s not reflected in the documentation above._

The first change we want to make is to replace the while(true) construct with a method that we can [cancel][3]. Using a [CancellationTokenSource][4], we can instead loop while that token is not cancelled.

<pre>private CancellationTokenSource _cancellationTokenSource;

public override void Run()
{
	Trace.WriteLine("SafeWorker - Entry point called", "Information");
	_cancellationTokenSource = new CancellationTokenSource();
	var token = _cancellationTokenSource.Token;

	while (!token.IsCancellationRequested)
	{
		Trace.WriteLine("SafeWorker - Starting some work", "Information");
		DoWork();
		Trace.WriteLine("SafeWorker - Finished some work", "Information");
		token.WaitHandle.WaitOne(10000);	// sleep 10s or exit early if cancellation is signalled
		// ...</pre>

Replacing the Thread.Sleep with a [WaitOne()][5] call will allow us to reduce the time to cancel. Unless it receives a signal (cancellation), the token will wait the specified number of milliseconds before continuing. Moving the WaitOne to the end ensures that if a cancellation is signaled, we won&#8217;t pick up one last bit of work before exiting.

The other piece of the equation is making the OnStop wait until the we have safely exited the loop. We can achieve this by creating a &#8220;Safe to exit&#8221; WaitHandle that is only set after successfully exiting the loop. The OnStop will Cancel via the CancellationToken, then wait for the &#8220;Safe to exit&#8221; token to be set before returning.

<pre>private CancellationTokenSource _cancellationTokenSource;
private ManualResetEvent _safeToExitHandle;

public override void Run()
{
	Trace.WriteLine("SafeWorker - Entry point called", "Information");
	_cancellationTokenSource = new CancellationTokenSource();
	_safeToExitHandle = new ManualResetEvent(false);
	var token = _cancellationTokenSource.Token;

	while (!token.IsCancellationRequested)
	{
		Trace.WriteLine("SafeWorker - Starting some work", "Information");
		DoWork();
		Trace.WriteLine("SafeWorker - Finished some work", "Information");
		token.WaitHandle.WaitOne(10000);	// sleep 10s or exit early if cancellation is signalled
	}

	Trace.WriteLine("SafeWorker - Ready to exit", "Information");
	_safeToExitHandle.Set();	// cleanly exited the main loop
}

// ...

public override void OnStop()
{
	Trace.WriteLine("SafeWorker - OnStop Called", "Information");
	_cancellationTokenSource.Cancel();
	_safeToExitHandle.WaitOne();
	Trace.WriteLine("SafeWorker - OnStop Complete, Exiting Safely", "Information");
}</pre>

Now if we run this like the BasicWorker above, hitting the Suspend button in the middle of the DoWork call, we see the system takes the time to exit out safely:

**Diagnostic Trace Output**

<pre>Information: 000.0s - SafeWorker - Entry point called
Information: 000.1s - SafeWorker - Starting some work
Information: 010.1s - SafeWorker - Finished some work
Information: 020.1s - SafeWorker - Starting some work
Information: 030.1s - SafeWorker - Finished some work
Information: 040.1s - SafeWorker - Starting some work
Information: 046.4s - SafeWorker - OnStop Called
Information: 050.1s - SafeWorker - Finished some work
Information: 050.1s - SafeWorker - Ready to exit
Information: 050.1s - SafeWorker - OnStop Complete, Exiting Safely</pre>

_note: I later added timestamps to the Trace output for readability_

We can see the OnStop call come in in the middle of our Starting some Work/Finished some work output, but instead of exiting immediately, the worker calmly finished up it&#8217;s work and then announced it was ready to exit (by setting the _safeToExitHandle WaitHandle).

## Wrapping Up

Letting your application die in the middle of an operation is typically not a good idea. 

The example code for this post is available on github at [tarwn/AzureWorkerRole_Cancellation][6]. After finishing the code samples above, I went back and add seconds elapsed to the trace output message. I didn&#8217;t update the code samples above because it would have only served to distract from the real code.

I haven&#8217;t posted on Azure as much as I probably should have, given how much of my time I spend working with it. Expect to see more posts on this in the upcoming months.

 [1]: http://magnusmartensson.com/howto-wait-in-a-workerrole-using-system-timers-timer-and-system-threading-eventwaithandle-over-system-threading-thread-sleep "HowTo wait in a WorkerRole using Timer and EventWaitHandle over Thread.Sleep"
 [2]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx "RoleEntryPoint.OnStop, MSDN"
 [3]: http://msdn.microsoft.com/en-us/library/dd997364.aspx "Cancellation in Managed Threads, MSDN"
 [4]: http://msdn.microsoft.com/en-us/library/system.threading.cancellationtokensource.aspx "CancellationTokenSource, MSDN"
 [5]: http://msdn.microsoft.com/en-us/library/cc189907.aspx "WaitOne, MSDN"
 [6]: https://github.com/tarwn/AzureWorkerRole_Cancellation "tarwn/AzureWorkerRole_Cancellation, GitHub"