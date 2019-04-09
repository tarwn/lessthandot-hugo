---
title: 'Threadsafe Incrementing in C#'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2014-07-02T10:54:49+00:00
ID: 2799
url: /index.php/desktopdev/mstech/csharp/threadsafe-incrementing-in-c/
views:
  - 12172
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
tags:
  - asynchronous
  - threading

---
Recently I've had the opportunity to review a number of different parallel C# methods that were performing work over a collection of items. Nearly all of them have used ++ to increment parent or global variables from inside threaded contexts. Unfortunately the ++ operator [is not guaranteed to be threadsafe in C#][1].

So let's take a simple code example using ++ and convert it to a threadsafe one.

## The Wrong Way To Do It

This is unrealistic example, I'm simply taking a list of work and sleeping for a random amount of time as a substitute for some real work. Pretend that it's doing something complex like database work, file conversions, or something else useful and time consuming.

```C#
public int DoWorkPoorly(List<string> workToProcess)
{
    int statusCounter = 0;
    int totalCount = workToProcess.Count;

    Parallel.ForEach(workToProcess, (work) =>
    {
        var r = new Random();
        Thread.Sleep((int)(r.NextDouble() * 10));

        Console.WriteLine(String.Format("Completed {0} of {1} items on thread {2}", 
                                        ++statusCounter, 
                                        totalCount, 
                                        Thread.CurrentThread.ManagedThreadId));
    });

    Console.WriteLine(String.Format("Completed {0} of {1}  items on thread {2}", 
                                    statusCounter, 
                                    totalCount,
                                    Thread.CurrentThread.ManagedThreadId));
    return statusCounter;
}
```
Call this with 10 or 100 items, and you may not see a problem. When I extend this to 1,000 or 10,000 then I start consistently losing up to 1.5% of the increments.

For a progress bar, this is probably ok, but for program logic or attempting to report an accurate number of results, this isn't going to work.

## Interlocked.Increment

Luckily, there is a built in method we can use to safely increment that shared integer.

```C#
public int DoWorkInterlocked(List<string> workToProcess)
{
    int statusCounter = 0;
    int totalCount = workToProcess.Count;

    Parallel.ForEach(workToProcess, (work) =>
    {
        var r = new Random();
        Thread.Sleep((int)(r.NextDouble() * 10));
        
        Interlocked.Increment(ref statusCounter);
        Console.WriteLine(String.Format("Completed {0} of {1}  items on thread {2}", 
                                        statusCounter, 
                                        totalCount, 
                                        Thread.CurrentThread.ManagedThreadId));
    });

    Console.WriteLine(String.Format("Completed {0} of {1}  items on thread {2}", 
                                    statusCounter, 
                                    totalCount, 
                                    Thread.CurrentThread.ManagedThreadId));
    return statusCounter;
}
```
Interlocked provides some methods to handle incrementing, decrementing, adding 64-bit values (which also isn't threadsafe), and so on. On a similar run of 100,000 items, this method is safe where the first one was not.

## Decouple Your Output

There is one final step. The prior examples push status messages to the console from whatever thread we happen to be on. Behind the scenes, they are fighting over access to the console, writing in non-sequential order, and writing more updates than we really need. At the end of the day, we want an accurate total number and enough updates to show us (humans) that it's making progress, we don't need all the messages and we especially don't need all of our little workers fighting over a single constrained resource.

If we take the responsibility for reporting status out of the parallel action and instead spin it off onto it's own task, we preserve our accurate total but have more control over reporting progress at a reasonable pace (and always in order). And in this particular case, as a by product of not fighting over a constrained resource, we gain an absurd amount of performance.

```C#
public int DoWorkInterlockedWithAsyncStatus(List<string> workToProcess)
{
    var statusCounter = 0;
    int totalCount = workToProcess.Count;

    // monitor and output progress
    var cancellationTokenSource = new CancellationTokenSource();
    var outputTask = Task.Factory.StartNew(() => {
        while (!cancellationTokenSource.IsCancellationRequested) 
        {
            Console.WriteLine(String.Format("Completed {0} of {1}  items on thread {2}", 
                                            statusCounter, 
                                            totalCount, 
                                            Thread.CurrentThread.ManagedThreadId));

            cancellationTokenSource.Token.WaitHandle.WaitOne(5);
	}

        Console.WriteLine(String.Format("Completed {0} of {1}  items on thread {2}", 
                                         statusCounter, 
                                         totalCount, 
                                         Thread.CurrentThread.ManagedThreadId));
    });

    Parallel.ForEach(workToProcess, (work) =>
    {
        var r = new Random();
        Thread.Sleep((int)(r.NextDouble() * 10));

        Interlocked.Increment(ref statusCounter);
    });

    cancellationTokenSource.Cancel();
    outputTask.Wait();
    return statusCounter;
}
```
Separating the two responsibilities nets us a an order of magnitude performance increase, but more importantly it moves that responsibility off to the side where it can be managed independently. If the human needs more or less frequent updates, or if we need to report that update to a specific thread (like the UI thread), we have a single place to manage it from that doesn't add drag to the actual work being done.

 [1]: http://stackoverflow.com/questions/4628243/is-the-operator-thread-safe "Eric Lippert's explanation on StackOverflow"