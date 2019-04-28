---
title: Why is it important to know that using has no empty catch and check for null in the finally?
author: Christiaan Baes (chrissie1)
type: post
date: 2012-10-21T06:58:00+00:00
ID: 1764
excerpt: Knowing that Using does not use an empty catch is important because it changes the whole working of the code.
url: /index.php/desktopdev/mstech/why-is-it-important-to/
views:
  - 1432
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - using
  - vb.net

---
## Introduction

In my previous post I examined what the Using code does. And why the 2005 version of the MSDN article might have been wrong. 

But why is it important to know that Using is not using a catch?

## The catch

Here is the code that we saw in the article.

```vbnet
Dim s2 = New FileStream("test2", FileMode.Create)
        Try
            Console.WriteLine("before exception")
            Throw New Exception()
            Console.WriteLine("after exception")
        Catch ex As Exception

        Finally
            s2.Dispose()
        End Try
```
The above code will just go merrily on it&#8217;s way, it won&#8217;t really stop and it is swallowing the exception. The user of that code does not know something bad happened and thinks everything is just fine. The user does not know there is a line after the exception he was supposed to get. He is blissfully unaware. 

But we are glad to say that Using does not do that.

So this code.

```vbnet
Using s1 = New FileStream("test1", FileMode.Create)
            Console.WriteLine("before exception")
            Throw New Exception()
            Console.WriteLine("after exception")
        End Using```
will throw an exception.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Using/Using1.png?mtime=1350809372"><img alt="" src="/wp-content/uploads/users/chrissie1/Using/Using1.png?mtime=1350809372" width="615" height="403" /></a>
</div>

## The isnot nothing

But the isnot nothing bit in the finally statement is useful to know too.

Because this code.

```vbnet
Dim s2 = New FileStream("test2", FileMode.Create)
        Try
            s2 = Nothing
        Finally
            s2.Dispose()
        End Try```
would have given you this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/Using/Using2.png?mtime=1350809728"><img alt="" src="/wp-content/uploads/users/chrissie1/Using/Using2.png?mtime=1350809728" width="581" height="418" /></a>
</div>

but the using statement does not cause this.

```vbnet
Using s1 = New FileStream("test1", FileMode.Create)
            s2 = Nothing
        End Using```
## Conclusion

Sometimes it pays to look just a bit further and not believe the first hit you get on Google.