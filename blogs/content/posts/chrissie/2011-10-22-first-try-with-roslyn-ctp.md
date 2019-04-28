---
title: 'First try with Roslyn CTP: Reading sourcefiles.'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-10-22T16:32:00+00:00
ID: 1354
excerpt: |
  Introduction
  
  Roslyn has been long awaited, especially in the VB.Net world. Since we need it to make some kind of VBCop. Of course this does not mean that Roslyn will be available in the next version of Visual studio yet. But we are hopeful for the ve&hellip;
url: /index.php/desktopdev/mstech/first-try-with-roslyn-ctp/
views:
  - 4098
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - roslyn ctp
  - vb.net

---
## Introduction

Roslyn has been long awaited, especially in the VB.Net world. Since we need it to make some kind of [VBCop][1]. Of course this does not mean that Roslyn will be available in the next version of Visual studio yet. But we are hopeful for the version after that.

## Installing

Installing Roslyn CTP is easy. Just got to the download page, download it (duh) and then install it (duh again).
  
I guess if your a programmer that these steps should be childsplay. You might notice that Roslyn is stil a codename. I&#8217;m guessing the official version will be named, .Net compiler services. Or they might come up with something original this time around&#8230;nah probably not. 

## Documentation

You can find [documentation][2] on MSDN.

## Reading source files

Now that you have Roslyn installed you will see that you have a whole slew of new projecttypes at your disposal.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn.png?mtime=1319305610"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn.png?mtime=1319305610" width="985" height="691" /></a>
</div>

I will start with a Console application.

I then created a new class in a separate file that looks like this.

```vbnet
Public Class Class1
    Public Property string1 As String
    Public Property string2 As String
End Class
```
And what I want is to get the properties out of that.

First I read the text of the file and hand it to Roslyn to disect.

```vbnet
Option Strict Off

Imports System.IO

Module Module1

    Sub Main()
        Dim sourcetext As String
        Using streamReader= New StreamReader("F:MyDocumentsVisual Studio 2010ProjectsConsoleApplication5Class1.vb")
            sourcetext = streamReader.ReadToEnd
        End Using

        Dim tree = SyntaxTree.ParseCompilationUnit(sourcetext)

    End Sub

End Module
```
The streamreader reads the file and puts the content in the sourcetext variable. We then use the SyntaxTree.ParseCompilationUnit to make Roslyn do it&#8217;s thing.

After this it is quite easy to get the properties out of there.

```vbnet
Dim tree = SyntaxTree.ParseCompilationUnit(sourcetext)

        Dim root As CompilationUnitSyntax = tree.Root

        Dim properties = root.DescendentNodes.OfType(Of PropertyStatementSyntax)()

        For Each prop In properties
            Console.WriteLine(prop.GetText)
            Console.WriteLine(prop.Identifier.GetFullText)
        Next
        Console.ReadLine()```
First we need to get the root of our class, which we can get with tree.Root. We then get the properties via the descendentnodes and we tell it to get all nodes of type PropertyStatementSyntax. We then just write the result out on the Console.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn2.png?mtime=1319307178"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn2.png?mtime=1319307178" width="707" height="423" /></a>
</div>

The identifier will get you the name of the property. You can also get the return type and so much more. Like attributes and what not.

This would be the shorter version of the above.

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

End Module
```
## Conclusion

That looks promising, but Roslyn can do so much more. And what happens when the sourcefile does not compile? More things to find out, more things to learn. I can already think of many things you can do with this.

 [1]: /index.php/DesktopDev/MSTech/vbcop-or-project-roslyn
 [2]: http://www.microsoft.com/download/en/details.aspx?id=27745