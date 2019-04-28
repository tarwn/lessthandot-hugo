---
title: VB.Net List.Select did not give me the result I expected
author: Christiaan Baes (chrissie1)
type: post
date: 2010-06-18T09:38:18+00:00
ID: 735
url: /index.php/desktopdev/mstech/vb-net-list-select-did-not-give-me-the-r/
views:
  - 7473
categories:
  - Microsoft Technologies
tags:
  - count
  - linq
  - select
  - vb.net

---
I think this was the first time I ever used the [Select extension method][1]. And it gave me an unexpected result.

First I created a Person Class.

```vbnet
Public Class Person
    Private _name As String

    Public Sub New()
        _name = "person1"
    End Sub

    Public Sub New(ByVal Name As String)
        _name = Name
    End Sub

    Public Property Name() As String
        Get
            Return _name
        End Get
        Set(ByVal value As String)
            _name = value
        End Set
    End Property
End Class
```
And what I wanted was to get a count of all the persons whose name was Test1. 

So I created myself a testclass.

```vbnet
Imports NUnit.Framework

&lt;testfixture()&gt; _
Public Class TestIlistSelect

    Private _persons As List(Of Person)

    &lt;SetUp()&gt; _
    Public Sub Setup()
        _persons = New List(Of Person)
        _persons.Add(New Person("Test1"))
        _persons.Add(New Person("Test2"))
        _persons.Add(New Person("Test3"))
    End Sub
End Class```
And the first test I did was this.

```vbnet
&lt;Test()&gt; _
    Public Sub If_Select_Name_As_Test1_Gives_1_Element()
        Assert.AreEqual(1, _persons.Select(Function(x) x.Name = "Test1").Count)
    End Sub```
Result

> <span class="MT_red">NUnit.Framework.AssertionException: Expected: 1<br /> But was: 3</span> 

Not good.

Ok, next attempt.

```vbnet
&lt;Test()&gt; _
    Public Sub If_Select_Name_As_Test1_Gives_1_Element_2()
        Assert.AreEqual(1, (_persons.Select(Function(x) x.Name = "Test1")).Count)
    End Sub```
Same result.

Next up.

```vbnet
&lt;Test()&gt; _
    Public Sub If_Select_Name_As_Test1_Gives_1_Element_3()
        Dim results = _persons.Select(Function(x) x.Name = "Test1")
        Assert.AreEqual(1, results.Count)
    End Sub```
And again nothing.

Time to look at what it is doing by debugging it. 

I thought Select was returning an array of Persons. But it isn&#8217;t it is returning an array of booleans. Where it gives you the result of the statement. 

So I actually got the following as a result.

> True
  
> False
  
> False 

Now on to the next test, just to see if I am right.

```vbnet
&lt;Test()&gt; _
    Public Sub If_Select_Name_As_Test1_Gives_1_Element_4()
        Dim results = _persons.Select(Function(x) x.Name = "Test1")
        Dim i = 0
        For Each result In results
            If result = True Then
                i += 1
            End If
        Next
        Assert.AreEqual(1, i)
    End Sub```
And yes this gives me <span class="MT_green">Success</span>.

Woohoo.

But while digging through the different extension methods I also found this one.

```vbnet
&lt;Test()&gt; _
    Public Sub If_Count_Name_As_Test1_Gives_1_Element_3()
        Assert.AreEqual(1, _persons.LongCount(Function(x) x.Name = "Test1"))
    End Sub```
Which gives me the result I want in a shorter fashion and more readable way.

> <span class="MT_red">All the above because I misunderstood what the Select was supposed to give me. Alex told me to use the Where method instead which gives me the result I wanted. I do not think that the Select method is very intuitive.</span>

 [1]: http://msdn.microsoft.com/en-us/library/bb548891.aspx