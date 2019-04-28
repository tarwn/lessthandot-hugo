---
title: A good book and the observer pattern for VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2009-04-13T11:40:50+00:00
ID: 384
excerpt: "I am reading this very good book. It's called Head first Design Patterns by Eric Freeman &amp; Elisabeth Freeman. It covers all the basic design patterns an OO-developer could ever use. Actualy, if you are doing OO programming you might see that you wer&hellip;"
url: /index.php/architect/introductionarchitecturedesign/a-good-book-and-the-observer-pattern-for/
views:
  - 16373
categories:
  - Introduction to Architecture and Design
tags:
  - observer pattern
  - vb.net

---
I am reading this very good book. It&#8217;s called [Head first Design Patterns by Eric Freeman & Elisabeth Freeman][1]. It covers all the basic design patterns an OO-developer could ever use. Actualy, if you are doing OO programming you might see that you were already using some of these patterns but didn&#8217;t know it. Alle the patterns are quite well explained in a simple matter. And we all know what Einstein said about explaining things simple. But for those of you who didn&#8217;t.

> [“If you can&#8217;t explain it simply, you don&#8217;t understand it well enough”][2]

So clearly Eric and Elisabeth have grocked design patterns.

But there is a little but for this book. All the code samples are explained with Java code as an example and in the beginning of the book they state that all examples will work just as well with C#. Which for the most part they do. 

But the [Observer pattern][3] in Java is always implemented with an abstract observable also called subject and an interface for the observer which holds the update method. 

I&#8217;m not going to explain in detail what the observer pattern is. You can read more about that in wikipedia or elsewhere on the Google.

What I am however goigng to show you is that in .Net and here VB.Net you would use events to do the observer observable thing in a much more effecient way.

Let&#8217;s say I have a class Observable that has a event called NotifyObservers and several classes Observer that hols a reference to Observable than I would have the same thing as the Observer pattern. At first sight it might look different because our Observable doesn&#8217;t seem to have a reference to it&#8217;s observers but this is taken care of behind the scenes. In C# the difference is a little smaller. So now some code.

```vbnet
Public Class Observable
Public Event Update()

Public Sub DoSomething()
  RaiseEvent Update() 
End Sub
End Class```
AS you can see this is a very simmple class with one public method and one public Event to which the observers can subscribe or handle the event. Now how do they subscribe to an event in VB.Net. That&#8217;s easy.

```vbnet
Public Class Observer1
Private WithEvents _Observable as Observable
Public Sub New(Byval Observable as Observable)
  _Observable = Observable
End Sub

Private Sub HandleUpdate() Handles _Observable.Update
   Console.WriteLine("I did it")
End Sub

End Class```
The WithEvents keyword here is very important since it makes sure we can handle events coming from the Observable Class. The Method HandleUpdate is decorated with the keywor Handles which means that if the Observable raises the event Update that the method HandleUpdate will take care of it.

If we create a little tesenvironment like so.

```vbnet
Module Module1

    Sub Main()
        Dim _Observable As New Observable
        Dim _Observer1 As New Observer1(_Observable)
        _Observable.DoSomething()
        Console.ReadLine()
    End Sub

End Module```
This will just print &#8220;I did it&#8221; in the console.

And we can create a second class that looks pretty much the same.

```vbnet
Public Class Observer2
Private WithEvents _Observable as Observable
Public Sub New(Byval Observable as Observable)
  _Observable = Observable
End Sub

Private Sub HandleUpdate() Handles _Observable.Update
   Console.WriteLine("I did it too")
End Sub

End Class```
And adapt our test implementation.

```vbnet
Module Module1

    Sub Main()
        Dim _Observable As New Observable
        Dim _Observer1 As New Observer1(_Observable)
        Dim _Observer2 As New Observer2(_Observable)
        _Observable.DoSomething()
        Console.ReadLine()
    End Sub

End Module```
Now it just prints &#8220;I did it&#8221; followed by &#8220;I did it too&#8221;

And all this by just calling _Observable.DoSomething and nothing else. Of course I could refactor this a bit, since I did it is the same in both classes.

I could for instance just pass the string via the update event. For this we change our Observable Class to.

```vbnet
Public Class Observable
Public Event Update(Byval Message as String)

Public Sub DoSomething()
  RaiseEvent Update("I did it") 
End Sub
End Class```
This means that I of course have to update the Observers too.

```vbnet
Public Class Observer1
    Private WithEvents _Observable As Observable
    Public Sub New(ByVal Observable As Observable)
        _Observable = Observable
    End Sub

    Private Sub HandleUpdate(ByVal Message As String) Handles _Observable.Update
        Console.WriteLine(Message)
    End Sub
End Class
```
```vbnet
Public Class Observer2
    Private WithEvents _Observable As Observable
    Public Sub New(ByVal Observable As Observable)
        _Observable = Observable
    End Sub

    Private Sub HandleUpdate(ByVal Message As String) Handles _Observable.Update
        Console.WriteLine(Message & " too")
    End Sub
End Class
```
The result of our code is exactly the same as before. But see how the event now takes a parameter and how the HandleUpdate methods on the observers has to implement that parameter.

As you have perhaps already seen the Observer pattern is widely used in .Net. And I think you can come up with plenty of usefull things for it too.

 [1]: http://www.amazon.com/First-Design-Patterns-Elisabeth-Freeman/dp/0596007124
 [2]: http://thinkexist.com/quotation/if_you_can-t_explain_it_simply-you_don-t/186838.html
 [3]: http://en.wikipedia.org/wiki/Observer_pattern