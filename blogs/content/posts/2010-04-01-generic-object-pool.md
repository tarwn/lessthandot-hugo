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

<pre>/// <summary>
/// Represents a pool of objects with a size limit.
/// </summary>
/// <typeparam name="T">The type of object in the pool.</typeparam>
public sealed class ObjectPool<T> : IDisposable
	where T : new()
{
	private readonly int size;
	private readonly object locker;
	private readonly Queue<T> queue;
	private int count;


	/// <summary>
	/// Initializes a new instance of the ObjectPool class.
	/// </summary>
	/// <param name="size">The size of the object pool.</param>
	public ObjectPool(int size)
	{
        if (size <= 0)
        {
            const string message = "The size of the pool must be greater than zero.";
            throw new ArgumentOutOfRangeException("size", size, message);
        }

		this.size = size;
		locker = new object();
		queue = new Queue<T>();
	}


	/// <summary>
	/// Retrieves an item from the pool. 
	/// </summary>
	/// <returns>The item retrieved from the pool.</returns>
	public T Get()
	{
		lock (locker)
		{
			if (queue.Count > 0)
			{
				return queue.Dequeue();
			}

			count++;
			return new T();
		}
	}

	/// <summary>
	/// Places an item in the pool.
	/// </summary>
	/// <param name="item">The item to place to the pool.</param>
	public void Put(T item)
	{
		lock (locker)
		{
			if (count < size)
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

	/// <summary>
	/// Disposes of items in the pool that implement IDisposable.
	/// </summary>
	public void Dispose()
	{
		lock (locker)
		{
            count = 0;
			while (queue.Count > 0)
			{
				using (queue.Dequeue() as IDisposable)
				{

				}
			}
		}
	}
}</pre>