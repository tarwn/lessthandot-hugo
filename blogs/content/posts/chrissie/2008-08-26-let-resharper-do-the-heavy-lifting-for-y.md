---
title: Let Resharper do the heavy lifting for you.
author: Christiaan Baes (chrissie1)
type: post
date: 2008-08-26T08:54:41+00:00
ID: 113
url: /index.php/desktopdev/mstech/let-resharper-do-the-heavy-lifting-for-y/
views:
  - 5794
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - equals
  - generate
  - gethashcode
  - properties
  - resharper
  - vb.net

---
[Reshaper][1] is a cool tool that can save you plenty of time (unless you start blogging about it of course ;-))

I will give the examples in VB.Net but this will also work in C#.

We all know what a DomainEntity looks like, right?

Ok so here is a simple example.

```vbnet
Public Class person
    Private _id As Guid
    Private _name As String
End Class```
First thing to do is create properties for it.
  
For this we use the [Reshaper][1] generate option, which is available via the shortcut **Alt+Ins** or via the menu **Resharper->Generate**.

This brings up the following contextmenu:

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/ResharperGenerate.jpg" alt="" title="" width="315" height="270" />
</div>

We choose Read-only properties:

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/ResharperGeneratereadonlyproperties.jpg" alt="" title="" width="501" height="499" />
</div>

And get this as a result:

```vbnet
Public Class person
    Private _id As Guid

    Overridable Public ReadOnly Property _idProperty() As Guid
        Get
            Return _id
        End Get
    End Property

    Private _name As String
End Class
```
Not ideal, but a timesaver none the less.

We do the same but then as a normal property for name.

And get this as a result:

```vbnet
Public Class person
    Private _id As Guid

    Overridable Public ReadOnly Property _idProperty() As Guid
        Get
            Return _id
        End Get
    End Property

    Private _name As String

    Overridable Public Property _nameProperty() As String
        Get
            Return _name
        End Get
        Set (ByVal value As String)
            _name = value
        End Set
    End Property
End Class
```
But now for the most important part. The overriding of the Equals and GetHashCode methods.

So we do Alt+Ins again and choose Equality members and get this dialog. We choose name and some other things that seem useful.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/ResharperGenerateEqualitymembers.jpg" alt="" title="" width="502" height="502" />
</div>

And we get this as a result:

```vbnet
Public Class person
    Implements IEquatable(Of person)
    Private _id As Guid

    Overridable Public ReadOnly Property _idProperty() As Guid
        Get
            Return _id
        End Get
    End Property

    Private _name As String

    Public Overloads Function Equals (ByVal obj As person) as Boolean
        If ReferenceEquals (Nothing, obj) Then Return False
        if ReferenceEquals (Me, obj) Then Return True
        Return Equals (obj._name, _name)

    End Function

    Public Overloads Overrides Function Equals (ByVal obj As Object) as Boolean
        If ReferenceEquals (Nothing, obj) Then Return False
        if ReferenceEquals (Me, obj) Then Return True
        If Not Equals (obj.GetType(), GetType (person)) Then Return False
        Return Equals (DirectCast (obj, person))

    End Function

    Public Overrides Function GetHashCode() as Integer
        If _name IsNot Nothing Then Return _name.GetHashCode()
        Return 0

    End Function

    Overridable Public Property _nameProperty() As String
        Get
            Return _name
        End Get
        Set (ByVal value As String)
            _name = value
        End Set
    End Property
End Class```
Cool, right, in a few clicks we got a whole lot of code. And a whole lot of working code to boot. It isn&#8217;t pretty, but there are things you can do about that.

There is also Formatting members which creates a ToString. 

Which creates something like this.

```vbnet
Public Overrides Function ToString() as String
        Return string.Format ("_id: {0}, _name: {1}", _id, _name)
    End Function```
I don&#8217;t know who came up with the names but they should look at Eclipse, it is much clearer there. And the property names aren&#8217;t what I would expect, a return to hungarian comes to mind.

 [1]: http://www.jetbrains.com