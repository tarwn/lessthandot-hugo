---
title: TTDing an interview question
author: Christiaan Baes (chrissie1)
type: post
date: 2009-07-30T07:45:31+00:00
ID: 525
url: /index.php/desktopdev/mstech/ttding-an-interview-question/
views:
  - 1992
categories:
  - Microsoft Technologies
tags:
  - interview question
  - puzzle
  - tdd
  - vb.net

---
Today I noticed [this older post][1] on [stackoverflow][2] and the first thing I thought, this would be great if done in a TDD manner. Very short too.

The question is 

> **Design a function f, such that:</p> 
> 
> f(f(n)) == -n
> 
> Where n is a 32 bit signed integer; you can&#8217;t use complex numbers arithmetic.
> 
> If you can&#8217;t design such a function for the whole range of numbers, design it for the largest range possible.</strong></blockquote> 
> 
> So you have to have a number _n_, run it through a method twice an get _-n_ as the result. In fact negating the result.
> 
> 1 becomes -1 and -1 becomes 1. usually remainder 0.
> 
> Hey I just got my test cases.
> 
> so first test case is to see if 1 becomes -1
> 
> ```vbnet
Imports NUnit.Framework
> 
> &lt;TestFixture()&gt; _
> Public Class TestFunctionF
> 
>     &lt;Test()&gt; _
>     Public Sub testfunctiongivesnegativewhengivinnegative()
>         Dim fclass As New Fclass
>         Assert.AreEqual(-1, fclass.f(fclass.f(1)))
>     End Sub
> End Class```
> use some resharper magic and I get this.
> 
> ```vbnet
Public Class Fclass
>     Shared n As Integer
> 
>     Public Function f(ByVal o As Integer) As Integer
>         Throw new NotImplementedException()
>     End Function
> End Class```
> Run the test and get a red light.
> 
> The simplest I could think of to solve the puzzle was this.
> 
> ```vbnet
Public Class Fclass
>     Shared n As Integer
> 
>     Public Function f(ByVal o As Integer) As Integer
>         n += 1
>         If n = 1 Then
>             Return -o
>         Else
>             n = 0
>             Return o
>         End If
>     End Function
> End Class```
> Sorry for all the bad naming of variables, but that made the test pass.
> 
> Now let&#8217;s see if test 2 passes also
> 
> ```vbnet
&lt;Test()&gt; _
>     Public Sub testfunctiongivespositivewhengivennegative()
>         Dim fclass As New Fclass
>         Assert.AreEqual(1, fclass.f(fclass.f(-1)))
>     End Sub```
> and it does.
> 
> Now for test number 3 to see if it gives 0 when needed.
> 
> ```vbnet
&lt;Test()&gt; _
>    Public Sub testfunctiongiveszerowhengivenzero()
>         Dim fclass As New Fclass
>         Assert.AreEqual(0, fclass.f(fclass.f(0)))
>     End Sub```
> and it does.
> 
> In the end, perhaps this wasn&#8217;t the best case for TDD since we only had one red, but that was because the simplest solution seemed to work from the word go for all solutions.

 [1]: http://stackoverflow.com/questions/731832/interview-question-ffn-n
 [2]: http://stackoverflow.com