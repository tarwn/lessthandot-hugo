---
title: To interface or not to interface that is the question
author: Christiaan Baes (chrissie1)
type: post
date: 2012-01-27T05:55:00+00:00
ID: 1508
excerpt: "I suspect that if you are reading this blog then you are using an IoC container to resolve your dependencies. If you do not and you just read this blog because you think I'm an awesome guy then that is fine too, but you should now take the time to learn how to use IoC/DI."
url: /index.php/desktopdev/mstech/to-interface-or-not-to/
views:
  - 3091
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - interface
  - ioc

---
I suspect that if you are reading this blog then you are using an IoC container to resolve your dependencies. If you do not and you just read this blog because you think I&#8217;m an awesome guy then that is fine too, but you should now take the time to learn how to use IoC/DI.

For those of you using IoC, I wonder how many of you have this.

```vbnet
Public Interface IInterface1
  Sub DoSomething()
End Interface

Public Class Class1
  Implements IInterface1

  Public sub DoSomething() Implements IInterface1.DoSomething()
  End Sub
End Class```
All this in the hope that someday you will get another class that implements IInterface1. But will you ever get a second implementation, think very carefully now, you know that if the answer is no, that you should call YAGNI. 

And with most containers you can easily go without the interface since they will let you register a class without the interface. And whenever you do need that second implementation you can just use Resharper and it will take care of it.

I know that in the past I made to much interface I never was going to use anyway. And although programming to interfaces is nice it also adds some overhead. More code is more maintenance.

So there are pros and cons to both approaches. But at this point in time I am of the opinion that you should use them wisely. I might change my mind again sometime soon. I am open to discussion.