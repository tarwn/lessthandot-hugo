---
title: 'First try with Roslyn CTP: Refactoring – Move class to its own file part 2'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-10-23T10:36:00+00:00
ID: 1357
excerpt: |
  Introduction
  
  In part 1 of trying to move the classes to their own files, I made sure I got the classes I needed. Now I'm going to Write out those classes and remove them from where they were before.
  
  Writing the classes to their own file
  
  The eas&hellip;
url: /index.php/desktopdev/mstech/first-try-with-roslyn-ctp-3/
views:
  - 6666
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - roslyn ctp
  - vb.net

---
## Introduction

In [part 1 of trying to move the classes to their own files][1], I made sure I got the classes I needed. Now I&#8217;m going to Write out those classes and remove them from where they were before.

## Writing the classes to their own file

The easy bit is writing out class3 and partial class 1 to their own files.

```vbnet
Option Strict Off

Imports System.IO

Module Module1

    Sub Main()
        Const foldername As String = "F:MyDocumentsVisual Studio 2010ProjectsConsoleApplication5"
        Dim sourcetext As String
        Using streamReader = New StreamReader(Path.Combine(foldername, "Class1.vb"))
            sourcetext = streamReader.ReadToEnd
        End Using

        Dim tree = SyntaxTree.ParseCompilationUnit(sourcetext)

        Dim root As CompilationUnitSyntax = tree.Root

        Dim classes =
                From e In root.DescendentNodes.OfType(Of ClassStatementSyntax)()
                Where (From f In e.Modifiers Select f.Kind.GetText).Contains("Public")
                Select e

        For Each classstatement In classes
            Console.WriteLine("Class: " & classstatement.Identifier.GetText)
            Dim tree2 = SyntaxTree.ParseCompilationUnit(classstatement.GetFullText)
            Dim filename = Path.Combine(foldername, classstatement.Identifier.GetText & ".vb")
            For Each modifier1 In classstatement.Modifiers
                Console.WriteLine(modifier1.Kind)
                Console.WriteLine(modifier1.Kind.GetText)
                If modifier1.Kind.GetText = "Partial" Then
                    filename = Path.Combine(foldername, classstatement.Identifier.GetText & "_partial.vb")
                End If
            Next
            File.WriteAllText(filename, classstatement.Parent.GetText)
        Next


        Console.ReadLine()
    End Sub

End Module
```
With this as the result.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn8.png?mtime=1319371213"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn8.png?mtime=1319371213" width="1337" height="279" /></a>
</div>

I just create a new syntaxtree <code class="codespan">Dim tree2 = SyntaxTree.ParseCompilationUnit(classstatement.GetFullText)</code> and then add a <code class="codespan">File.WriteAllText(filename, classstatement.Parent.GetText)</code> and I&#8217;m done. Of course this is the easy option since I don&#8217;t have to deal with namespaces or imports or options. 

## Adding the namespace

So let&#8217;s say all these classes belong to the same namespace, how would I make sure my resulting syntaxes also all have the same namespace.

```vbnet
Namespace Test
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
End Namespace
```
You cannot just add something to a syntaxtree since syntaxtrees are imutable.
  
So we will have to make sure we get the namespace out of our original syntaxtree and then just add that to our sourcetext. 

Something like this.

```vbnet
Option Strict Off

Imports System.IO

Module Module1

    Sub Main()
        Const foldername As String = "F:MyDocumentsVisual Studio 2010ProjectsConsoleApplication5"
        Dim sourcetext As String
        Using streamReader = New StreamReader(Path.Combine(foldername, "Class1.vb"))
            sourcetext = streamReader.ReadToEnd
        End Using

        Dim tree = SyntaxTree.ParseCompilationUnit(sourcetext)

        Dim root As CompilationUnitSyntax = tree.Root

        Dim classes =
                From e In root.DescendentNodes.OfType(Of ClassStatementSyntax)()
                Where (From f In e.Modifiers Select f.Kind.GetText).Contains("Public")
                Select e
        Dim _namespace = From e In root.DescendentNodes.OfType(Of NamespaceStatementSyntax)() Select e

        For Each classstatement In classes
            Console.WriteLine("Class: " & classstatement.Identifier.GetText)
            Dim classandnamespace = _namespace(0).GetText & Environment.NewLine & classstatement.Parent.GetText & Environment.NewLine & "End NameSpace"

            Dim tree2 = SyntaxTree.ParseCompilationUnit(classandnamespace)

            Dim filename = Path.Combine(foldername, classstatement.Identifier.GetText & ".vb")
            For Each modifier1 In classstatement.Modifiers
                Console.WriteLine(modifier1.Kind)
                Console.WriteLine(modifier1.Kind.GetText)
                If modifier1.Kind.GetText = "Partial" Then
                    filename = Path.Combine(foldername, classstatement.Identifier.GetText & "_partial.vb")
                End If
            Next
            File.WriteAllText(filename, classandnamespace)
        Next


        Console.ReadLine()
    End Sub

End Module
```
This is now our result.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn9.png?mtime=1319373259"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn9.png?mtime=1319373259" width="1290" height="299" /></a>
</div>

## Conclusion

You can do a lot of cool stuff with Roslyn and I&#8217;m only scratching the surface here. And I bet there are lots of different ways to do the above.

 [1]: /index.php/DesktopDev/MSTech/first-try-with-roslyn-ctp-2