---
title: New features in Visual Basic 10
author: Christiaan Baes (chrissie1)
type: post
date: 2008-10-30T09:55:21+00:00
ID: 188
url: /index.php/desktopdev/mstech/new-features-in-visual-basic-10/
views:
  - 3501
categories:
  - Microsoft Technologies
tags:
  - happy
  - vb.net

---
YEEEEEEEEEEEEHA 😀 the spelling checker didn&#8217;t like that one), here are some of the new features in VB 10.
  
And yes, I did sound happy there, because some of these features are really not optional but much needed. Like the multi-line lambda and the sub lambda. 

You can find the whole video on [Channel9][1]

Multi-line lambdas will look like this

```vbnet
myfunction.lambdathing(Function(e) e.dosomething
                                   e.dosomethingelse
                       End Function)```
Or like this 

```vbnet
myfunction.lambdathing(Sub() dosomething
                             dosomethingelse
                       End Sub)```
Now this will also be possible. And I&#8217;m very happy about that.

```vbnet
myfunction.lambdathing(Sub() dosomething)```
They will also support auto properties and collection initializers, neither of which I really needed. 

I&#8217;m so happy, I think I will stick with VB for a while. Although all the catching up to C# thing is annoying. I just hope it doesn&#8217;t become a VS 2011 or later version. Let&#8217;s make it a VS 2010 that came out in 2009 version shall we?

And here is some other stuff that should be possible.

```vbnet
Private Sub SomeMethod()
     Dim _counter as Integer = 1
     AddHandler Button1.Click, Sub()
                                      _counter += 1
                                      TextBox1.text = _counter
                                  End Sub```
```vbnet
Private Sub SomeMethodRunningInAnotherThread()
     Me.Dispatcher.Invoke(Normal, Sub()
                                      'Do some other stuff
                                      SomeTextBox.Text = "Test"
                                  End Sub)
 End Sub```
Info found after some random surfing on [StackOverflow][2]

 [1]: http://channel9.msdn.com/posts/Dan/Lucian-Wischik-and-Lisa-Feigenbaum-Whats-new-in-Visual-Basic-10/
 [2]: http://stackoverflow.com/questions/249314/multiline-lambdas-in-vb-10