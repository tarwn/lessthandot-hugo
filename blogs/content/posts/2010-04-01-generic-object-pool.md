---
title: Generic Object Pool
author: chaospandion
type: post
date: 2010-04-01T23:08:41+00:00
url: /index.php/desktopdev/mstech/generic-object-pool/
views:
  - 10684
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - .net
  - 'c#'
  - object pool
  - sockets

---
Recently I was working with some socket programming using the SocketAsyncEventArgs class. When writing high performance .NET code it is a good idea to keep object creation to a minimum. Instead of creating a new SocketAsyncEventArgs instance every time an action was performed I decided to put together a simple generic object pool. I hope you&#8217;ll try it out and leave me some feedback. 

<pre>/// &lt;summary&gt;
/// Represents a pool of objects with a size limit.
/// &lt;/summary&gt;
/// &lt;typeparam name="T"&gt;The type of object in the pool.&lt;/typeparam&gt;
public sealed class ObjectPool&lt;T&gt; : IDisposable
	where T : new()
{
	private readonly int size;
	private readonly object locker;
	private readonly Queue&lt;T&gt; queue;
	private int count;


	/// &lt;summary&gt;
	/// Initializes a new instance of the ObjectPool class.
	/// &lt;/summary&gt;
	/// &lt;param name="size"&gt;The size of the object pool.&lt;/param&gt;
	public ObjectPool(int size)
	{
        if (size &lt;= 0)
        {
            const string message = "The size of the pool must be greater than zero.";
            throw new ArgumentOutOfRangeException("size", size, message);
        }

		this.size = size;
		locker = new object();
		queue = new Queue&lt;T&gt;();
	}


	/// &lt;summary&gt;
	/// Retrieves an item from the pool. 
	/// &lt;/summary&gt;
	/// &lt;returns&gt;The item retrieved from the pool.&lt;/returns&gt;
	public T Get()
	{
		lock (locker)
		{
			if (queue.Count &gt; 0)
			{
				return queue.Dequeue();
			}

			count++;
			return new T();
		}
	}

	/// &lt;summary&gt;
	/// Places an item in the pool.
	/// &lt;/summary&gt;
	/// &lt;param name="item"&gt;The item to place to the pool.&lt;/param&gt;
	public void Put(T item)
	{
		lock (locker)
		{
			if (count &lt; size)
			{
				queue.Enqueue(item);
			}
			else
			{
				using (item as IDisposable)
				{
					count--;
				}
			}
		}
	}

	/// &lt;summary&gt;
	/// Disposes of items in the pool that implement IDisposable.
	/// &lt;/summary&gt;
	public void Dispose()
	{
		lock (locker)
		{
            count = 0;
			while (queue.Count &gt; 0)
			{
				using (queue.Dequeue() as IDisposable)
				{

				}
			}
		}
	}
}</pre>