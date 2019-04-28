---
title: Threading isn’t easy
author: Christiaan Baes (chrissie1)
type: post
date: 2009-03-18T16:30:52+00:00
ID: 351
url: /index.php/desktopdev/mstech/threading-isn-t-easy/
views:
  - 3907
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - 'c#'
  - threading
  - vb.net

---
Whatever you might think threading isn&#8217;t easy, I don&#8217;t think our brain can really understand it just because using multiple threads takes away the logic of the sequence. You don&#8217;t know and aren&#8217;t going to know when your new thread is going to execute. You are at the mercy of the OS. So that is one thing to take into account. But on the other hand threading is awesome. It can solve performance problems without to much coding and in these days of multiple cores it&#8217;s bound to help. But try to refrain yourself from using them as much as possible they are (or should be) a last resort. Debugging can be a pain if you use them unwisely. 

Learning about multithreading can be a pain too but some people just know how to explain things in a (nearly) simple matter. I would strongly suggest reading this [Threading in C# by Joseph Albahari][1]. As you can see this article is for C# and allthough most code will work in VB.Net some won&#8217;t. 

Lets just look at this little example.

```vbnet
class WriteTest {

  static void Main() {

  Thread t = new Thread (delegate() { WriteText ("Hello"); });

  t.Start();

}

static void WriteText (string text) { Console.WriteLine (text); }
  }

}```
That uses an anonymous delegate seen in this part 

```vbnet
delegate() { WriteText ("Hello"); }```
. 

Which can be converted to this in C# 4.0 using a lambda expression.

```csharp
class Program
    {
        static void Main(string[] args)
        {
            var t = new Thread(() =&gt; WriteText("Hello"));

            t.Start();
        }

        static void WriteText(string text) { Console.WriteLine(text); }
    }```
Now as far as I know VB doesn&#8217;t have anonymous delegates yet. So this will get tricky to translate. But I think reflector can handle this.

First lets see what reflector tells me my code looks like in C#.

```csharp
internal class Program
{
    // Methods
    private static void Main(string[] args)
    {
        new Thread(delegate {
            WriteText("Hello");
        }).Start();
        Console.ReadLine();
    }

    private static void WriteText(string text)
    {
        Console.WriteLine(text);
    }
}

 
```
That is interesting it changed the lamda to an anonymous delegate in other words the compiler did that for us.

Now lets look at what reflector says about its VB.Net counterpart.

```vbnet
Friend Class Program
    ' Methods
    Private Shared Sub Main(ByVal args As String())
        New Thread(Function 
            Program.WriteText("Hello")
        End Function).Start
        Console.ReadLine
    End Sub

    Private Shared Sub WriteText(ByVal [text] As String)
        Console.WriteLine([text])
    End Sub

End Class

```
That&#8217;s interesting it&#8217;s using a multiline lambda. Not something that will work in VB9 so reflector is of no help. So there is no way you can translate the above C# to VB.Net just yet, not using lambdas or anonymous delegates anyway. I&#8217;m hopefull in VB10 it will. But since the CTP expired I can&#8217;t try this anymore. I hope they give us a beta to test soon.

But a little word of warning from the field. If you are going to use multithreading in your application than also test on a singlecore machine, they are still out there and they can have the opposite effect for your so well crafted code. I know most developers these days have at least a dual core but some users don&#8217;t have those and then the you could see that your program with the multiple threads is actualy slower than the normal sequential version.

 [1]: http://www.albahari.com/threading/