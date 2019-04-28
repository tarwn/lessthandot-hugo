---
title: System.IO.Path.GetFileName does not do what it advertises
author: Christiaan Baes (chrissie1)
type: post
date: 2010-08-10T09:16:51+00:00
ID: 867
url: /index.php/desktopdev/mstech/system-io-path-getfilename-does-not-do-w/
views:
  - 5730
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - system.io.getfilename
  - vb.net

---
## Introduction

System.IO.Path.GetFileName does not really do what the name says in certain circumstances. But we can use that behavior none the less.

## Prerequisites

I have setup a consoleapplication with 2 folders and a file in each folder.
  
Something like this.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/path/Path.png" alt="" title="" width="157" height="129" />
</div>

I set the file properties of Class1.vb and Class2.vb to Build Action -> None and Copy to Output Directory -> Copy Always.

## Files

This is what you would use GetFileName for in most circumstances because that is the logical thing to do.

```vbnet
Dim directories = System.IO.Directory.GetDirectories(Environment.CurrentDirectory)
        For Each directory In directories
            Dim files = System.IO.Directory.GetFiles(directory)
            For Each File In files
                Console.WriteLine(System.IO.Path.GetFileName(File))
            Next
        Next
        Console.ReadLine()```
```csharp
var directories = System.IO.Directory.GetDirectories(Environment.CurrentDirectory);
        foreach(var directory In directories)
        {
            var files = System.IO.Directory.GetFiles(directory);
            foreach(var File In files)
            {
                Console.WriteLine(System.IO.Path.GetFileName(File));
            }
        }
        Console.ReadLine()```
Thsi would give you the following result.

> Class1.vb
  
> Class2.vb

## Directories

But then I added this line of code.

<code class="codespan">Console.WriteLine(System.IO.Path.GetFileName(directory))</code>

Like so.

```vbnet
Dim directories = System.IO.Directory.GetDirectories(Environment.CurrentDirectory)
        For Each directory In directories
            Dim files = System.IO.Directory.GetFiles(directory)
            For Each File In files
                Console.WriteLine(System.IO.Path.GetFileName(File))
            Next
            Console.WriteLine(System.IO.Path.GetFileName(directory))
        Next
        Console.ReadLine()```
```csharp
var directories = System.IO.Directory.GetDirectories(Environment.CurrentDirectory);
        foreach(var directory In directories)
        {
            var files = System.IO.Directory.GetFiles(directory);
            foreach(var File In files)
            {
                Console.WriteLine(System.IO.Path.GetFileName(File));
            }
            Console.WriteLine(System.IO.Path.GetFileName(directory));
        }
        Console.ReadLine()```
This gives the following result.

> Class1.vb
  
> NewFolder1
  
> Class2.vb
  
> NewFolder2

If I go read the [MSDN article][1] on this.

It says:

> Return Value
  
> Type: System.String
  
> The characters after the last directory character in path. If the last character of path is a directory or volume separator character, this method returns String.Empty. If path is Nothing, this method returns Nothing.

So what I think they are doing is to check for the last directory or volumeseparator and then take what ever comes after that as the filename. I wonder why they do not check if that is a real file? That would be a very simple check and would really return a filename. Now it just returns anything that looks like a filename. Perhaps they should change it to <span class="MT_red">GetPossibleFileName</span> because that&#8217;s what it does.

## Conclusion

When writing an API choose your methodnames carefully so they tell the user what they can expect. GetFileName does not return what I expect. Thank you for listening.

 [1]: http://msdn.microsoft.com/en-us/library/system.io.path.getfilename.aspx