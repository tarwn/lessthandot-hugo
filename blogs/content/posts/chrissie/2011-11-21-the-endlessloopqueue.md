---
title: The EndlessLoopQueue
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-21T11:26:00+00:00
ID: 1394
excerpt: |
  I named it EndlessLoopQueue because I couldn't think of another name for it.
  
  You can put items on this queue but when you enqueue an item it will be re-added to the end of the queue therefor you will end up in some kind of endless loop (hence EndlessLoopQueue, yeah I am kinda clever).
url: /index.php/desktopdev/mstech/the-endlessloopqueue/
views:
  - 1439
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - endlessloopqueue
  - vb.net

---
I named it EndlessLoopQueue because I couldn&#8217;t think of another name for it.

You can put items on this queue but when you enqueue an item it will be re-added to the end of the queue therefor you will end up in some kind of endless loop (hence EndlessLoopQueue, yeah I am kinda clever).

It&#8217;s possible that this exists but I could not find it in the .Net 4.0 Framework. 

I wrote the class in C# and the tests in VB.Net just to confuse you.

Here is the EndlessLoopQueue.

```csharp
using System;
using System.Collections;
using System.Collections.Generic;

namespace TDB2009.View.BigScreen.Controls
{
    public class EndlessLoopQueue&lt;T&gt;: IEnumerable&lt;T&gt;
    {
        readonly IList&lt;T&gt; _buffer;
        int _currentItem;
        readonly int _bufferSize;
        readonly object _lock = new object();

        public EndlessLoopQueue(int bufferSize)
        {
            _buffer = new List&lt;T&gt;(bufferSize);
            _bufferSize = bufferSize;
        }

        public bool IsEmpty
        {
            get { return _buffer.Count == 0; }
        }

        public bool IsFull
        {
            get { return _buffer.Count == _bufferSize; }
        }

        public T Dequeue()
        {
            lock (_lock)
            {
                T dequeued = _buffer[_currentItem];
                _currentItem = NextPosition(_currentItem);
                return dequeued;
            }
        }

        public T this[int columnIndex]
        {
            get
            {
                if (IsEmpty) throw new InvalidOperationException("No Items In Queue");
                if (columnIndex &gt; _buffer.Count -1) throw new ArgumentException("Index is not in this EndlessLoopQueue");
                return _buffer[columnIndex];  
            } 
            set
            {
                Enqueue(value);
            }
        }

        private int NextPosition(int position)
        {
            if(position+1 &gt; _buffer.Count-1) return 0;
            return position + 1;
        }

        public void Add(T toAdd)
        {
            Enqueue(toAdd);
        }

        public void Enqueue(T toAdd)
        {
            lock (_lock)
            {
                if (IsFull) throw new InvalidOperationException("The pattern is full.");
                _buffer.Add(toAdd);
            }
        }

        public IEnumerator&lt;T&gt; GetEnumerator()
        {
            return _buffer.GetEnumerator();
        }

        IEnumerator IEnumerable.GetEnumerator()
        {
            return GetEnumerator();
        }
    }
}```
And here are the tests.

```vbnet
Imports NUnit.Framework
Imports TDB2009.View.BigScreen.Controls

Namespace Controls
    &lt;TestFixture()&gt;
    Public Class TestCircularBuffer

        &lt;Test()&gt;
        Public Sub IfBufferCanEnqueue()
            Dim c As New EndlessLoopQueue(Of String)(2)
            c.Enqueue("test")
        End Sub

        &lt;Test()&gt;
        Public Sub IfBufferCanUseListInitializer()
            Dim c = New EndlessLoopQueue(Of String)(2) From {"test", "test1"}
            Assert.AreEqual("test", c.Dequeue())
            Assert.AreEqual("test1", c.Dequeue())
        End Sub

        &lt;Test()&gt;
        Public Sub IfBufferCanDequeueIfBufferIsNotFull()
            Dim c As New EndlessLoopQueue(Of String)(2)
            c.Enqueue("test")
            Assert.AreEqual("test", c.Dequeue())
            Assert.AreEqual("test", c.Dequeue())
        End Sub

        &lt;Test()&gt;
        Public Sub IfBufferCanDequeueIfBufferIsFull()
            Dim c As New EndlessLoopQueue(Of String)(2)
            c.Enqueue("test")
            c.Enqueue("test1")
            Assert.AreEqual("test", c.Dequeue())
            Assert.AreEqual("test1", c.Dequeue())
            Assert.AreEqual("test", c.Dequeue())
        End Sub

        &lt;Test()&gt;
        Public Sub IfBufferThrowsArgumentExceptionWhenIndexDoesNotExist()
            Dim c As New EndlessLoopQueue(Of String)(2)
            c.Enqueue("test")
            c.Enqueue("test1")
            Assert.Throws(Of ArgumentException)(Sub() c(2).ToString())
        End Sub

        &lt;Test()&gt;
        Public Sub IfCanGetElementFromIndex()
            Dim c As New EndlessLoopQueue(Of String)(2)
            c.Enqueue("test")
            c.Enqueue("test1")
            Assert.AreEqual("test", c(0))
            Assert.AreEqual("test1", c(1))
        End Sub

        &lt;Test()&gt;
        Public Sub IfIsFullReturnsTrueWhenQueueIsFull()
            Dim c As New EndlessLoopQueue(Of String)(2)
            c.Enqueue("test")
            c.Enqueue("test1")
            Assert.IsTrue(c.IsFull)
        End Sub

        &lt;Test()&gt;
        Public Sub IfIsFullReturnsFalseWhenQueueIsNotFull()
            Dim c As New EndlessLoopQueue(Of String)(2)
            c.Enqueue("test")
            Assert.IsFalse(c.IsFull)
        End Sub

        &lt;Test()&gt;
        Public Sub IfIsEmptyReturnsTrueWhenQueueIsEmpty()
            Dim c As New EndlessLoopQueue(Of String)(2)
            Assert.IsTrue(c.IsEmpty)
        End Sub

        &lt;Test()&gt;
        Public Sub IfIsEmptyReturnsFalseWhenQueueIsNotEmpty()
            Dim c As New EndlessLoopQueue(Of String)(2)
            c.Enqueue("test")
            c.Enqueue("test1")
            Assert.IsFalse(c.IsEmpty)
        End Sub

        &lt;Test()&gt;
        Public Sub IfAddingToAFullQueueThrowsInvalidOperationException()
            Dim c As New EndlessLoopQueue(Of String)(2)
            c.Enqueue("test")
            c.Enqueue("test1")
            Assert.Throws(Of InvalidOperationException)(Sub() c.Enqueue("test2"))
            Assert.IsTrue(c.IsFull)
        End Sub
    End Class
End Namespace```
It&#8217;s more or less based [on this][1], but functionally they are totally different.

 [1]: http://www.blackwasp.co.uk/CircularBuffer.aspx