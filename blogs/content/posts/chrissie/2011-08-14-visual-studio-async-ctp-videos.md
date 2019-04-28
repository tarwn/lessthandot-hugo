---
title: Visual Studio Async CTP videos
author: Christiaan Baes (chrissie1)
type: post
date: 2011-08-14T06:09:00+00:00
ID: 1299
excerpt: |
  There a some nice visual studio Async videos on MSDN.
  
  There are videos for VB.Net and C#. 
  
  VB.Net
  
  
    Introduction to the Async CTP 5 minutes 48 seconds
    Polling and Cancellation 7 minutes 24 seconds
    Offloading Work with TaskEx.Run 4 minut&hellip;
url: /index.php/desktopdev/mstech/visual-studio-async-ctp-videos/
views:
  - 5226
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - vb.net

---
There a some nice [visual studio Async videos on MSDN][1].

There are videos for VB.Net and C#. 

## VB.Net

  1. Introduction to the Async CTP 5 minutes 48 seconds
  2. Polling and Cancellation 7 minutes 24 seconds
  3. Offloading Work with TaskEx.Run 4 minutes 43 seconds
  4. Concurrent Downloading with TaskEx.WhenAll 3 minutes 6 seconds
  5. Refactoring Functionality into a Library 4 minutes 41 seconds

## C#

  1. Introduction to the Async CTP 5 minutes 49 seconds
  2. Concurrent Downloading with TaskEx.WhenAll 3 minutes 02 seconds
  3. Offloading Work with TaskEx.Run 4 minutes 42 seconds
  4. Polling and Cancellation 7 minutes 17 seconds
  5. Refactoring Functionality into a Library 4 minutes 54 seconds

You can download the [Async CTP SP1 refresh from MSDN][2]. And you can shoot the person that named it while you are there.

You can also download the &#8220;[Visual Basic Language Specification for Asynchronous Functions and Iterators][3]&#8220;. 

And this is on page one.

> Iterator and Async Methods in Visual Basic
> 
> The “Microsoft Visual Studio Async CTP” introduces async methods and iterator methods to Visual Basic, along with their four new contextual keywords: Async, Await, Iterator, Yield.
> 
> Async methods are used for long-running computations. This language specification does not aim to teach asynchrony – the best place for that is through watching Anders Hejlsberg’s PDC 2010 talk, and then by working through the walkthrough and samples. Unfortunately there is not yet a large body of further training material: VB’s form of asynchrony was largely inspired by F# Async Workflows in Visual Studio 2010 and had not been widely used before then. I want to stress just one point:
  
> Asynchrony is NOT the same as “running on a background thread”. In computing, as the word’s etymology suggests, asynchronous merely means “not [a-] at the same time [-synchronous]”. In other words you can invoke a function, and promptly get back a placeholder for the result, but the actual result of the function will be given to you not immediately but at some other later time. Background threads are one way to achieve asynchrony, but they’re not the only way, and are often not even the best way.
  
> What we are delivering in this Async CTP is a compositional (easy) way to make asynchronous calls in the Visual Basic language.
> 
> This Async CTP also introduces iterator methods to VB. Iterator methods are deeply related to async methods. Because we are introducing both at the same time, we have been able to reflect their underlying similarity by giving them similar syntax and semantics. VB Iterator methods are largely the same as those that already exist in C#. The differences that there are, are all motivated for reasons of similarity with async methods:
  
> 1. VB iterator methods require an explicit Iterator modifier;
  
> 2. VB iterator methods use the Yield keyword instead of yield return, and they use VB’s existing Return and Exit Function statements instead of yield break;
  
> 3. the VB Yield statement is allowed in the Try body of an exception handler with a Catch clause; and
  
> 4. VB allows anonymous iterator methods (lambdas) as well as top-level iterator methods.
> 
> This specification for iterator and async methods is written as a series of drop-in-replacements for sections in the existing Visual Basic 10 Language Specification, normally found in
  
> C:Program FilesMicrosoft Visual Studio 10.0VBSpecifications
> 
> &#8212; Lucian Wischik, VB Spec Lead, April 2010. 

Or you can [download the C# ones][4].

> Asynchronous operations are methods and other function members that may have most of their execution take place after they return. In .NET the recommended pattern for asynchronous operations is for them to return a task which represents the ongoing operation and allows waiting for its eventual outcome.
  
> Asynchronous operations are useful when multiple flows of control need to share the threads they run on. For instance they can be used to share a single user interface thread between multiple ongoing operations, or to service thousands of simultaneous ongoing requests on a server with a much smaller pool of threads.
  
> Traditionally, writing and composing asynchronous operations is difficult and error prone. It involves installing callbacks – sometimes called continuations – on other asynchronous operations to express all the logic that needs to happen after those operations completes. This alters, and usually significantly complicates, the structure of asynchronous code as compared to a corresponding synchronous program. 

Have fun!!

 [1]: http://msdn.microsoft.com/en-us/vstudio/hh378091.aspx
 [2]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=9983
 [3]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22388
 [4]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=23753