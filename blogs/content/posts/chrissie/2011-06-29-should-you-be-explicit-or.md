---
title: Should you be explicit or just use the defaults (beware contains VB.Net)
author: Christiaan Baes (chrissie1)
type: post
date: 2011-06-29T08:09:00+00:00
ID: 1235
excerpt: |
  "Know thou language" is something Shakespeare said ages ago. Or at least he should have. Knowing your language also means you know what it is going to do without explicitly telling it to. 
  
  For example.
  
  Module Module1
  	Sub Main()
  		Dim p As New H&hellip;
url: /index.php/desktopdev/mstech/should-you-be-explicit-or/
views:
  - 2391
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - vb.net

---
&#8220;Know thou language&#8221; is something Shakespeare said ages ago. Or at least he should have. Knowing your language also means you know what it is going to do without explicitly telling it to. 

For example.

```vbnet
Module Module1
	Sub Main()
		Dim p As New Hydrangea
		Console.ReadLine()
	End Sub

	Public Class Plant
		Public Sub New()
			Console.WriteLine("I am plant")
		End Sub
	End Class

	Public Class Hydrangea
		Inherits Plant

		Public Sub New()
			Console.WriteLine("I am hydrangea")
		End Sub
	End Class
	
End Module
```
Will print.

> I am Plant
  
> I am Hydrangea

Hold on there we never said it to say it was a plant. But by default VB.Net will call Mybase.New for you.

So the following code will do the same thing. We are just more explicit in stating it.

```vbnet
Module Module1
	Sub Main()
		Dim p As New Hydrangea
		Console.ReadLine()
	End Sub

	Public Class Plant
		Public Sub New()
			Console.WriteLine("I am plant")
		End Sub
	End Class

	Public Class Hydrangea
		Inherits Plant

		Public Sub New()
                        Mybase.New()
			Console.WriteLine("I am hydrangea")
		End Sub
	End Class
	
End Module
```
and this.

```vbnet
Module Module1
	Sub Main()
		Dim p As New Hydrangea
		Console.ReadLine()
	End Sub

	Public Class Plant
		Public Sub New()
			Console.WriteLine("I am plant")
		End Sub
	End Class

	Public Class Hydrangea
		Inherits Plant

	End Class
	
End Module
```
Will print 

> I am plant

Which would then be the same as this.

```vbnet
Module Module1
	Sub Main()
		Dim p As New Hydrangea
		Console.ReadLine()
	End Sub

	Public Class Plant
		Public Sub New()
			Console.WriteLine("I am plant")
		End Sub
	End Class

	Public Class Hydrangea
		Inherits Plant

		Public Sub New()
			Mybase.New()
		End Sub
	End Class
	
End Module
```
And would again print.

> I am plant

In the cases above I would just go for the default behavior because it suits me fine, but that is kind of the problem. I am now excepting that the person after me also knows that default behavior. Should I then really be doing that or should I be more explicit. I am also hoping that default behavior will never change. Is that a good thing, considering people at Microsoft tend to not be considerate all the time?

I have no good answer and I have no Cristal ball, but for the moment I would prefer the less code option and deal with the future when it comes.