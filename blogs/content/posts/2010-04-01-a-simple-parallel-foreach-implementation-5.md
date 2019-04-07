---
title: A Simple Parallel.ForEach Implementation in .NET 3.5
author: Alex Ullrich
type: post
date: 2010-04-01T12:25:00+00:00
excerpt: |
  In an application I'm working on, I had a need for a method to write a single object to several Lucene indexes in order to support different types of searches.  I ended up with a method that looks like this:
  public static void Accept<T>(T to_be_i&hellip;
url: /index.php/webdev/serverprogramming/a-simple-parallel-foreach-implementation-5/
views:
  - 19215
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Server Programming
tags:
  - .net
  - 'c#'
  - concurrency
  - mono

---
In an application I&#8217;m working on, I had a need for a method to write a single object to several [Lucene][1] indexes in order to support different types of searches. I ended up with a method that looks like this:

<pre>public static void Accept<T>(T to_be_indexed) 
{
	var indexers = StructureMap.ObjectFactory.GetAllInstances<Indexer<T>>();
	
	foreach (var x in indexers)
	{
		x.Index(to_be_indexed);
	}
}</pre>

Simple enough. Because of all the disk I/O involved, it seemed like this would be a good candidate for processing concurrently. I tried a few different options, but found manually creating an array of threads too awkward, and had some trouble getting the ThreadPool to wait properly. I was talking about this with [Matt][2] and he showed me a small class that he used for just this type of scenario called Countdown.

<pre>public class Countdown : IDisposable
{
    private readonly ManualResetEvent done;
    private volatile int current;

    public Countdown(int total)
    {
        current = total;
        done = new ManualResetEvent(false);
    }

    public void Signal()
    {
        lock (done)
        {
            if (current > 0 && --current == 0)
                done.Set();
        }
    }

    public void Wait()
    {
        done.WaitOne();
    }

    public void Dispose()
    {
        ((IDisposable)done).Dispose();
    }
} </pre>

I found this class **much** more predictable and easier to work with than a bunch of ManualResetEvents, so we started looking at how it could be used to solve the problem at hand. It was remarkably easy, and I can only recall one issue that we ran into. The end result looked like this:

<pre>public static void Accept<T>(T to_be_indexed) 
{
	var indexers = StructureMap.ObjectFactory.GetAllInstances<Indexer<T>>();
	
	using (var cd = new Countdown( indexers.Count ))
    {
        foreach (var current in indexers)
        {
            var captured = current;
            ThreadPool.QueueUserWorkItem(x =>
                {
                    captured.Index(to_be_indexed);
                    cd.Signal();
                });
        }
        cd.Wait();
    }
}</pre>

The problem we ran into was the lambda expression grabbing whatever &#8220;current&#8221; was defined as **_at execution time_**, causing the same index to be written to twice (and others not at all) in many cases. This is what forced us to add the line
  
<code class="codespan">var captured = current;</code>
  
and use captured rather than current within the lambda. I&#8217;ve gotta say, I **really** like the all the code in that using block for the countdown. It may be because I set rather low expectations with my hackish early attempts to get this done, but I like things very simple and this definitely fits the bill. But, as beautiful / simple as the code may be, I still don&#8217;t want to write it multiple times if I can avoid it. So I took a look at the signature for Parallel.ForEach in .net 4 and threw together a version that would work with my system (running on mono 2.4.2.3, mostly equivalent to .net 3.5). Here that is:

<pre>public static void ForEach<T>(IEnumerable<T> enumerable, Action<T> action)
{
    using (var cd = new Countdown( enumerable.Count() ))
    {
        foreach (var current in enumerable)
        {
            var captured = current;
            ThreadPool.QueueUserWorkItem(x =>
                {
                    action.Invoke(captured);
                    cd.Signal();
                });
        }
        cd.Wait();
    }
}</pre>

The only real changes were to use the Count() method on IEnumerable rather than the Count property on IList, and the change from
  
<code class="codespan">captured.Index(to_be_indexed);</code>
  
to
  
<code class="codespan">action.Invoke(captured);</code>
  
to allow execution of whatever method was passed in.

Then, the Accept method can be changed to look like this:

<pre>public static void Accept<T>(T to_be_indexed) 
{
	var indexers = StructureMap.ObjectFactory.GetAllInstances<Indexer<T>>();
	
	Parallel.ForEach(indexers, x => {
		x.Index(to_be_indexed);	
	});
}</pre>

I realize that this is a very naive implementation, and that Parallel.ForEach in .net 4 is probably doing things under the hood that I haven&#8217;t even considered (optimizing for different number of CPU&#8217;s and such) but I think / hope that the ThreadPool takes care of some of that for me. I&#8217;d love to hear any comments on how to make it better though. 

For another implementation, check out this Code Project article: [Poor Man&#8217;s Parallel ForEach][3]. We tested this implementation briefly (and far from scientifically), but found that the ThreadPool behaved a bit more consistently, was often a bit faster, and also required much less code to implement.

 [1]: http://incubator.apache.org/projects/lucene.net.html
 [2]: /index.php/All/?disp=authdir&author=225
 [3]: http://www.codeproject.com/KB/dotnet/PoorMansParallelForEach.aspx