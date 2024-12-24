---
title: VB.Net, bitwise operations and Enums (Enumerations)
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-05T06:45:00+00:00
ID: 1371
excerpt: |
  Bitwise operations are fun and binary math can be really usefull if you know how. At least you should know why a computer is much faster at calculations with floating point numbers than fixed point numbers. And if you don't know why that is you should stop all programming work right now and go and read up on it, it's important (for me anyway). 
  
  So I digressed, this post is not about floating point calculations. This post is about Enums and bitwise operations and how to take advantage of that knowledge.
url: /index.php/desktopdev/mstech/vb-net-bitwise-operations-and/
views:
  - 9393
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - bitwise opertations
  - enums
  - vb.net

---
## Introduction

Bitwise operations are fun and binary math can be really useful, if you know how. At least you should know why a computer is much faster at calculations with floating point numbers than fixed point numbers. And if you don&#8217;t know why that is, you should stop all programming work right now and go and read up on it, it&#8217;s important (for me anyway). 

So I digressed, this post is not about floating point calculations. This post is about Enums and bitwise operations and how to take advantage of that knowledge.

## The checkbox conundrum

I just know you have come across something like this in your life.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/enums/enums1.png?mtime=1320479235"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/enums/enums1.png?mtime=1320479235" width="240" height="207" /></a>
</div>

And we all know this needs a backing object. 

Something like this perhaps.

```vbnet
Public Class OptionsObject
    Public Property Name As String
    Public Property Option1 As Boolean
    Public Property Option2 As Boolean
    Public Property Option3 As Boolean
    Public Property Option4 As Boolean
End Class```
What if I have to save that to a database and I want to add an option later? Yup, I would have to ask the DBA to change the database, and we all know how long that takes.

What if I told you the following does exactly the same thing.

```vbnet
Public Class OptionsObject
    Public Property Name As String
    Public Property Options As OptionsEnum
    
    Public Enum OptionsEnum
        None = 0
        Option1 = 1
        Option2 = 2
        Option3 = 4
        Option4 = 8
        All = Option1 Or Option2 Or Option3 Or Option4
    End Enum
End Class```
At the very least your DBA would be more pleased, since we can now store that value in one tinyint instead of 4 bitfields. And we don&#8217;t have to change the database when we add or delete an option.

Let us dissect the above. 

## The dissection

In binary the above would look like this.

0000 = None = Decimal 0
  
0001 = Option1 = Decimal 1
  
0010 = Option2 = Decimal 2
  
0100 = Option3 = Decimal 4
  
1000 = Option4 = Decimal 8
  
1111 = All = Decimal 15

If I have checkbox 2 and 3 checked, I would have this value in my Options field.

And if you want to add more options you just add the next power of 2, being 16 and then 32 and so on. 

We would of course have unittests to check our theories.

```vbnet
&lt;Test()&gt;
    Public Sub IfEnum0IsNone()
        Assert.AreEqual(0, OptionsEnum.None)
    End Sub

    &lt;Test()&gt;
    Public Sub IfEnum1IsOption1()
        Assert.AreEqual(1, OptionsEnum.Option1)
    End Sub

    &lt;Test()&gt;
    Public Sub IfEnum2IsOption2()
        Assert.AreEqual(2, OptionsEnum.Option2)
    End Sub

    &lt;Test()&gt;
    Public Sub IfEnum4IsOption3()
        Assert.AreEqual(4, OptionsEnum.Option3)
    End Sub

    &lt;Test()&gt;
    Public Sub IfEnum8IsOption4()
        Assert.AreEqual(8, OptionsEnum.Option4)
    End Sub

    &lt;Test()&gt;
    Public Sub IfEnum15IsAll()
        Assert.AreEqual(15, OptionsEnum.All)
    End Sub```
## Storing the values

We want to store what we have in our view into our backingobject.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/enums/Enums2.png?mtime=1320480765"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/enums/Enums2.png?mtime=1320480765" width="240" height="207" /></a>
</div>

0110 = Option2 and Option3 are selected. in decimal that would be 6. 

Something like this perhaps.

```vbnet
Public Class Form1

    Private Sub CheckBox1_CheckedChanged(sender As System.Object, e As System.EventArgs) Handles CheckBox1.CheckedChanged, CheckBox2.CheckedChanged, CheckBox3.CheckedChanged, CheckBox4.CheckedChanged
        Optionscalc()
    End Sub

    Private Sub Optionscalc()
        Dim options As OptionsObject.OptionsEnum = OptionsObject.OptionsEnum.None
        If CheckBox1.Checked Then options = options Or OptionsObject.OptionsEnum.Option1
        If CheckBox2.Checked Then options = options Or OptionsObject.OptionsEnum.Option2
        If CheckBox3.Checked Then options = options Or OptionsObject.OptionsEnum.Option3
        If CheckBox4.Checked Then options = options Or OptionsObject.OptionsEnum.Option4
        TextBox1.Text = options.ToString
    End Sub
End Class
```
So now we can store our values to the backingobject easily. I&#8217;m sure you can come up with better ways to do this.

## Determining if your option is selected

Perhaps you want to show our users on load of the form what we already have in the database. So how do we determine which checkbox has to be checked?

That is easy. First let me show you some unittests I wrote to prove my point.

```vbnet
&lt;TestFixture()&gt;
Public Class Tests
    &lt;Test()&gt;
    Public Sub IfOption1AndOption2CanBeAdded()
        Dim options As OptionsObject.OptionsEnum = OptionsObject.OptionsEnum.Option1 Or OptionsObject.OptionsEnum.Option2
        Assert.IsTrue((OptionsObject.OptionsEnum.Option1 Or options) = options)
        Assert.IsTrue((OptionsObject.OptionsEnum.Option2 Or options) = options)
        Assert.IsFalse((OptionsObject.OptionsEnum.Option3 Or options) = options)
        Assert.IsFalse((OptionsObject.OptionsEnum.Option4 Or options) = options)
        Assert.IsFalse((OptionsObject.OptionsEnum.All Or options) = options)
    End Sub

    &lt;Test()&gt;
    Public Sub IfOption2AndOption4BeAdded()
        Dim options As OptionsObject.OptionsEnum = OptionsObject.OptionsEnum.Option4 Or OptionsObject.OptionsEnum.Option2
        Assert.IsFalse((OptionsObject.OptionsEnum.Option1 Or options) = options)
        Assert.IsTrue((OptionsObject.OptionsEnum.Option2 Or options) = options)
        Assert.IsFalse((OptionsObject.OptionsEnum.Option3 Or options) = options)
        Assert.IsTrue((OptionsObject.OptionsEnum.Option4 Or options) = options)
        Assert.IsFalse((OptionsObject.OptionsEnum.All Or options) = options)
    End Sub

    &lt;Test()&gt;
    Public Sub IfAddingthemAlltogethergivesAll()
        Dim options As OptionsObject.OptionsEnum = OptionsObject.OptionsEnum.Option4 Or OptionsObject.OptionsEnum.Option2 Or OptionsObject.OptionsEnum.Option1 Or OptionsObject.OptionsEnum.Option3
        Assert.IsTrue((OptionsObject.OptionsEnum.Option1 Or options) = options)
        Assert.IsTrue((OptionsObject.OptionsEnum.Option2 Or options) = options)
        Assert.IsTrue((OptionsObject.OptionsEnum.Option3 Or options) = options)
        Assert.IsTrue((OptionsObject.OptionsEnum.Option4 Or options) = options)
        Assert.IsTrue((OptionsObject.OptionsEnum.All Or options) = options)
    End Sub
End Class
```
In our form that would look something like this.

```vbnet
Private Sub Form1_Load(sender As System.Object, e As System.EventArgs) Handles MyBase.Load
        Dim options As OptionsObject.OptionsEnum = OptionsObject.OptionsEnum.Option2 Or OptionsObject.OptionsEnum.Option3
        If (options Or OptionsObject.OptionsEnum.Option1) = options Then CheckBox1.Checked = True
        If (options Or OptionsObject.OptionsEnum.Option2) = options Then CheckBox2.Checked = True
        If (options Or OptionsObject.OptionsEnum.Option3) = options Then CheckBox3.Checked = True
        If (options Or OptionsObject.OptionsEnum.Option4) = options Then CheckBox4.Checked = True
        TextBox1.Text = options.ToString
    End Sub```
Which would then look like this on load.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/enums/Enums2.png?mtime=1320480765"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/enums/Enums2.png?mtime=1320480765" width="240" height="207" /></a>
</div>

And again I&#8217;m sure you can find a way to make this better.

## Conclusion

Bitwise operations and Enums can be a nice thing to have, they make it easy to store things on the database size, because adding an option does not require us to change our database.