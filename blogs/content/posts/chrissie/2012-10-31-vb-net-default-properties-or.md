---
title: 'VB.Net default properties or indexers in C#'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-31T17:56:00+00:00
ID: 1776
excerpt: 'How default properties in VB.Net are the same as indexers in C#.'
url: /index.php/desktopdev/mstech/vb-net-default-properties-or/
views:
  - 4757
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - default property
  - indexers
  - vb.net

---
## Introduction

This post was inspired by [this post by Damian Guard][1] &#8220;8 things you probably didn’t know about C#&#8221;

The first point is that indexers can take params as a parameter so you can then give a list of items to get another list of items using the indexer.

## Default properties and indexers

Lets take this collection as an example of how an indexer works.

```vbnet
Dim _list = New List(of String) From {"test1", "test2"}```
An indexer let&#8217;s you do this.

```vbnet
_list(0)```
This is exactly the same thing as 

```vbnet
_list.Item(0)```
When we look at the collection and the item property this would look something like this.

```
Default Public Property Item(index As Integer) As String
            Get
                ' Code here
            End Get
            Set(value As String)
                ' Code here
            End Set
        End Property```
The Default keyword is the key here. It determines which property is the indexer and which you can use without specifying it&#8217;s name.

But you can use this in your normal classes to.

Just look at this example.

```vbnet
Imports System.Reflection

Module Module1

    Sub Main()
        Dim s As New SomeCollection
        Console.WriteLine(s(0))
        For Each s1 In s({0, 1, 2})
            Console.WriteLine(s1)
        Next
        Console.ReadLine()
    End Sub

    Public Class SomeCollection

        Private _list As IList(Of String)

        Public Sub New()
            _list = New List(Of String) From {"test1", "test2", "test3", "test4"}
        End Sub

        Default Public ReadOnly Property Item(index As Integer) As String
            Get
                Return _list(index)
            End Get
        End Property

        Default Public ReadOnly Iterator Property Item(index() As Integer) As IEnumerable(Of String)
            Get
                For Each i In index
                    Yield _list(i)
                Next
            End Get
        End Property

    End Class

End Module
```
So I can use instance(0) to get one element or use instance({0,2}) to get a list of elements.

But I can also add more possibilities.

```vbnet
Imports System.Reflection

Module Module1

    Sub Main()
        Dim s As New SomeCollection
        For Each s1 In s({"test1", "test3"})
            Console.WriteLine(s1)
        Next
        Console.WriteLine(s("test1"))
        Console.ReadLine()
    End Sub

    Public Class SomeCollection

        Private _list As IList(Of String)

        Public Sub New()
            _list = New List(Of String) From {"test1", "test2", "test3", "test4"}
        End Sub

        Default Public ReadOnly Property Item(index As Integer) As String
            Get
                Return _list(index)
            End Get
        End Property

        Default Public ReadOnly Property Item(index As String) As Integer
            Get
                Return _list.IndexOf(index)
            End Get
        End Property

        Default Public ReadOnly Iterator Property Item(index() As String) As IEnumerable(Of Integer)
            Get
                For Each i In index
                    Yield _list.IndexOf(i)
                Next
            End Get
        End Property

    End Class

End Module
```
I now use a string as the indexer. I could even combine the integer and the string one in one class. And so much more.

This is the whole thing combined and in C#.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            var s = new SomeCollection();
            Console.WriteLine(s[0]);
            foreach(var s1 in s[0,1,2])
            {
                Console.WriteLine(s1);
            }
            Console.WriteLine(s["test1"]);
            foreach (var s1 in s["test1", "test3"])
            {
                Console.WriteLine(s1);
            }
            Console.ReadLine();
        }
    }

    public class SomeCollection
    {

        private IList&lt;String&gt; _list;

        public SomeCollection()
        {
            _list = new List&lt;String&gt; {"test1", "test2", "test3", "test4"};
        }

        public String this[int index]
        {
            get { return _list[index]; }
        }

        public int this[String index]
        {
            get { return _list.IndexOf(index); }
        }

        public IEnumerable&lt;String&gt; this[params int[] index]
        {
            get { return index.Select(i =&gt; _list[i]); }
        }

        public IEnumerable&lt;int&gt; this[params String[] index]
        {
            get { return index.Select(i =&gt; _list.IndexOf(i)); }
        }
    }
}
```
And we see that in C# they use params. in VB.Net this is however not an option.

If you try this.

```vbnet
Default Public ReadOnly Iterator Property Item(ParamArray index() As String) As IEnumerable(Of Integer)
            Get
                For Each i In index
                    Yield _list.IndexOf(i)
                Next
            End Get
        End Property```
Then you will get an error stating.

> Properties with no required parameters cannot be declared &#8216;Default&#8217;.

## Conclusion

The default property looks very different in VB.Net then it is in C# but it is there. And I&#8217;m sure not to many of used it in the last year.

 [1]: http://damieng.com/blog/2012/10/29/8-things-you-probably-didnt-know-about-csharp