---
title: The law of demeter
author: Christiaan Baes (chrissie1)
type: post
date: 2008-10-14T05:07:10+00:00
ID: 176
url: /index.php/architect/designingsoftware/the-law-of-demeter/
views:
  - 6847
categories:
  - Designing Software
  - Introduction to Architecture and Design
tags:
  - lod
  - the law of demeter
  - vb.net

---
## Introduction

If you have been using an OO language to program with then you should have heard of the Law of Demeter. If you haven&#8217;t then you should read some of the articles I collected on Google.

  * [Wikipedia][1]
  * [The Paperboy, The Wallet, and The Law Of Demeter by David Bock][2]
  * [Introducing Demeter and its Laws][3]
  * [Entities and the Law of Demeter by Jimmy Bogard][4]



## Definition

It is actually very simple to me. But explaining it can be a bit more daunting. This is what you can find on [Wikipedia][1] about it.

> **When applied to object-oriented programs, the Law of Demeter can be more precisely called the “Law of Demeter for Functions/Methods” (LoD-F). In this case, an object, A, can request a service (call a method) of an object instance, B, but object A cannot “reach through” object B to access yet another object, C, to request its services. Doing so would mean that object A implicitly requires greater knowledge of object B’s internal structure. Instead, B’s class should be modified, if necessary, so that object A can simply make the request directly of object B, and then let object B propagate the request to any relevant subcomponents. Or A should have a direct reference to object C and make the call directly. If the law is followed, only object B knows its own internal structure.</p> 
> 
> More formally, the Law of Demeter for functions requires that a method M of an object O may only invoke the methods of the following kinds of objects:
> 
> 1. O itself
     
> 2. M&#8217;s parameters
     
> 3. Any objects created/instantiated within M
     
> 4. O&#8217;s direct component objects
> 
> In particular, an object should avoid invoking methods of a member object returned by another method. For many modern object oriented languages that use a dot as field identifier, the law can be stated simply as &#8220;use only one dot&#8221;. That is, the code &#8220;a.b.Method()&#8221; breaks the law where &#8220;a.Method()&#8221; does not. There is disagreement as to the sufficiency of this approach.</strong></blockquote> 
> 
> 
> 
> ## My side of the story
> 
> Let&#8217;s oversimplify this a bit. When you see 2 dots in a statement, then you know something is going to be wrong. Look at this:
> 
> ```vbnet
Dim _Person as Person = New Person
> Console.WriteLine(_Person.Addresses.Count)```
> In that example _Person.Addresses.Count is a smell. Why? Because Addresses could be null and when it is null it can give a nullreferenceexception and crash our program.
> 
> There are 2 ways around this problem.
> 
> First.
> 
> ```vbnet
Dim _Person as Person = New Person
> Try
>    Console.WriteLine(_Person.Addresses.Count)
> Catch ex as NullReferenceExceptionException
>    Console.WriteLine(0)
> End Try```
> or 
> 
> ```vbnet
Dim _Person as Person = New Person
> If _Person.Addresses IsNot Nothing then
>    Console.WriteLine(_Person.Addresses.Count)
> Else
>    Console.WriteLine(0)
> End If```
> What is wrong with this approach? Well every time you call Addresses.Count you have to check if Addresses isn&#8217;t null and print 0 instead. This gets boring very quickly and sometimes somebody might forget this little detail.
> 
> So we have a better solution. Namely, add a Method/Property to our Class Person. We name it CountAddresses for example and it would look something like this.
> 
> ```vbnet
Public Function CountAddresses() as Integer
>    If _Addresses IsNot Nothing Then
>       Return _Addresses.Count
>    Else 
>       Return 0
>    End If
> End Function```
> Now we can call CountAdresses instead of Addresses.Count and not have to fear NullReferenceExceptions anymore.
> 
> The downside of this is that you have to write more code in your classes. But less code in your calling classes.
> 
> So I guess you should use it wisely.
  
> 
> 
> ## When not
> 
> And when doesn&#8217;t the Law of Demeter apply?
> 
>   * Fluent interfaces (Unlimited amount of dots)
>   * Factory classes (You can have 2 dots there)
>   * Legacy systems (No laws apply in there)

 [1]: http://en.wikipedia.org/wiki/Law_of_Demeter
 [2]: http://www.ccs.neu.edu/research/demeter/demeter-method/LawOfDemeter/paper-boy/demeter.pdf
 [3]: http://www.cmcrossroads.com/bradapp/docs/demeter-intro.html
 [4]: http://www.lostechies.com/blogs/jimmy_bogard/archive/2008/07/07/entities-and-the-law-of-demeter.aspx