---
title: 'First try with Roslyn CTP: Refactoring – Move class to its own file'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-10-23T06:04:00+00:00
ID: 1356
excerpt: |
  Introduction
  
  In my previous attempts to work with Roslyn I installed Roslyn and read the sourcefiles. Now it's time to do some refactoring.
  
  Find the class to refactor
  
  I had made this file in my first attempts.
  
  Public Class Class1
      Public&hellip;
url: /index.php/desktopdev/mstech/first-try-with-roslyn-ctp-2/
views:
  - 2438
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - roslyn ctp
  - vb.net

---
## Introduction

In my previous attempts to work with Roslyn [I installed Roslyn][1] and [read the sourcefiles][2]. Now it&#8217;s time to do some refactoring.

## Find the class to refactor

I had made this file in my first attempts.

```vbnet
Public Class Class1
    Public Property string1 As String
    Public Property string2 As String

    Private Class Class2
        Public Property string3 As String
        Public Property string4 As String
    End Class
End Class

Public Class Class3
    Public Property string5 As String
    Public Property string6 As String
End Class

Partial Public Class Class1
    Public Property string7 As String
    Public Property string8 As String
End Class
```
I want to move Class3 and the partial Class1 out of that file and into their own files.

First I need to find Class3 and Class1.

For this I want to look for all the Classes that are public. Wich is fairly simple if you use the modifiers property.

```vbnet
Option Strict Off

Imports System.IO

Module Module1

    Sub Main()
        Dim sourcetext As String
        Using streamReader = New StreamReader("F:MyDocumentsVisual Studio 2010ProjectsConsoleApplication5Class1.vb")
            sourcetext = streamReader.ReadToEnd
        End Using

        Dim classes = From e In SyntaxTree.ParseCompilationUnit(sourcetext).Root.DescendentNodes.OfType(Of ClassStatementSyntax)()

        For Each classstatement In classes
            Console.WriteLine("Class: " & classstatement.Identifier.GetText)
            For Each modifier1 In classstatement.Modifiers
                Console.WriteLine(modifier1.Kind)
                Console.WriteLine(modifier1.Kind.GetText)
            Next
        Next
        Console.ReadLine()
    End Sub

End Module
```
This gives me the following result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/roslyn/roslyn6.png?mtime=1319355123"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/roslyn/roslyn6.png?mtime=1319355123" width="707" height="423" /></a>
</div>

As you can see I still have the Private class in that list, which I don&#8217;t want. So I get that out using this linq statement.

```vbnet
Dim classes = From e In SyntaxTree.ParseCompilationUnit(sourcetext).Root.DescendentNodes.OfType(Of ClassStatementSyntax)() Where (From f In e.Modifiers Select f.Kind.GetText).Contains("Public") Select e```
And then I don&#8217;t want the Class1 statement except if it is partial.

Which brings us to this linq statement.

```vbnet
Dim classes =
                From e In SyntaxTree.ParseCompilationUnit(sourcetext).Root.DescendentNodes.OfType(Of ClassStatementSyntax)()
                Where (From f In e.Modifiers Select f.Kind.GetText).Contains("Public") AndAlso (e.Identifier.GetText &lt;&gt; "Class1" OrElse (From f In e.Modifiers Select f.Kind.GetText).Contains("Partial"))
                Select e```
With this as the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/roslyn/roslyn7.png?mtime=1319356957"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/roslyn/roslyn7.png?mtime=1319356957" width="707" height="423" /></a>
</div>

Since this post is getting rather long already I will leave the reading and writing bit for the next post and the conclusion too.

 [1]: /index.php/DesktopDev/MSTech/first-try-with-roslyn-ctp
 [2]: /index.php/DesktopDev/MSTech/first-try-with-roslyn-ctp-1