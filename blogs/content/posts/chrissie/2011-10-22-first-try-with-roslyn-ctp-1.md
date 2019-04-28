---
title: 'First try with Roslyn CTP: Reading sourcefiles.part 2'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-10-22T17:42:00+00:00
ID: 1355
excerpt: |
  Introduction
  
  In part one of reading the sourcefile with Roslyn I installed Roslyn, made a new console project and read the content of this file. Now lets go a bit further.
  
  Property and class
  
  So I adapted my sourcefile to contain a bit more posi&hellip;
url: /index.php/desktopdev/mstech/first-try-with-roslyn-ctp-1/
views:
  - 1946
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - roslyn ctp
  - vb.net

---
## Introduction

In part one of reading the sourcefile with Roslyn I installed Roslyn, made a new console project and read the content of this file. Now lets go a bit further.

## Property and class

So I adapted my sourcefile to contain a bit more posibilities.

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
If I now run my previous example.

```vbnet
Imports System.IO
 
Module Module1
 
    Sub Main()
        Dim sourcetext As String
        Using streamReader = New StreamReader("F:MyDocumentsVisual Studio 2010ProjectsConsoleApplication5Class1.vb")
            sourcetext = streamReader.ReadToEnd
        End Using
 
        For Each prop In SyntaxTree.ParseCompilationUnit(sourcetext).Root.DescendentNodes.OfType(Of PropertyStatementSyntax)()
            Console.WriteLine(prop.GetText)
            Console.WriteLine(prop.Identifier.GetFullText)
        Next
        Console.ReadLine()
    End Sub
 
End Module```
I get the following result.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn3.png?mtime=1319311151"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn3.png?mtime=1319311151" width="707" height="423" /></a>
</div>

That is not a very usefull result since I have no idea where the properties are coming from. But that is easliy fixed since each property also has a parent property.

```vbnet
Imports System.IO

Module Module1

    Sub Main()
        Dim sourcetext As String
        Using streamReader = New StreamReader("F:MyDocumentsVisual Studio 2010ProjectsConsoleApplication5Class1.vb")
            sourcetext = streamReader.ReadToEnd
        End Using

        For Each prop In SyntaxTree.ParseCompilationUnit(sourcetext).Root.DescendentNodes.OfType(Of PropertyStatementSyntax)()
            Console.WriteLine("Property: " & prop.Identifier.GetText)
            If TypeOf prop.Parent Is ClassBlockSyntax Then
                Console.WriteLine("Belongs to: " & CType(CType(prop.Parent, ClassBlockSyntax).ChildNodes(0), ClassStatementSyntax).Identifier.GetText)
            End If

        Next
        Console.ReadLine()
    End Sub

End Module
```
Which gives this result.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn4.png?mtime=1319311335"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn4.png?mtime=1319311335" width="707" height="423" /></a>
</div>

You have to watch out because the parent is not a ClassStatementSyntax but a ClassBlockSyntax. The ClassBlockSyntax does not contain the identifier but it does contain a childnode that is a ClassStatemetSyntax which does contain the identifier. You could just print the whole syntaxtree to find this out, or RTFM.

Of course you can also use a bit more LINQ and get this.

```vbnet
Option Strict Off

Imports System.IO

Module Module1

    Sub Main()
        Dim sourcetext As String
        Using streamReader = New StreamReader("F:MyDocumentsVisual Studio 2010ProjectsConsoleApplication5Class1.vb")
            sourcetext = streamReader.ReadToEnd
        End Using

        Dim propclasses = From e In SyntaxTree.ParseCompilationUnit(sourcetext).Root.DescendentNodes.OfType(Of PropertyStatementSyntax)() Select New With {.PropertyName = e.Identifier.GetText, .ClassName = CType(CType(e.Parent, ClassBlockSyntax).ChildNodes(0), ClassStatementSyntax).Identifier.GetText}

        For Each propclass In propclasses.OrderBy(Function(x) x.ClassName)
            Console.WriteLine("Property: " & propclass.PropertyName)
            Console.WriteLine("Belongs to: " & propclass.ClassName)
        Next
        Console.ReadLine()
    End Sub

End Module
```
With a slightly different result as before.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn5.png?mtime=1319312474"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn5.png?mtime=1319312474" width="707" height="423" /></a>
</div>

## Conclusion

Once you got how the syntaxtree thing works it&#8217;s very easy to find what you need.