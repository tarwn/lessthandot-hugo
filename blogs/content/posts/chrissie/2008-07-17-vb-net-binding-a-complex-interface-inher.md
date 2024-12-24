---
title: 'VB.Net: Binding a complex interface(inherited) to a datagridview'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-07-17T09:07:30+00:00
ID: 86
url: /index.php/desktopdev/mstech/vb-net-binding-a-complex-interface-inher/
views:
  - 5666
categories:
  - Microsoft Technologies
  - VB.NET

---
Lets say I have a class teacher which inherits from a class person (hard to believe I know ;-)). And we want to bind a list of Iteachers (the interface) to a datagridview because that is the easiest way of getting things working for the datagridview. This will give a surprising (perhaps not) result.

First lets look at the Person class and IPerson interface.

```vbnet
Public Interface IPerson
    Property Name() As String
    Property FirstName() As String
End Interface

Public Class Person
    Implements IPerson
    Private _name As String
    Private _firstname As String

    Public Sub New()

    End Sub

    Public Sub New(ByVal Name As String, ByVal Firstname As String)
        _name = Name
        _firstname = Firstname
    End Sub

    Public Property Name() As String Implements IPerson.Name
        Get
            Return _name
        End Get
        Set(ByVal value As String)
            _name = value
        End Set
    End Property

    Public Property FirstName() As String Implements IPerson.FirstName
        Get
            Return _firstname
        End Get
        Set(ByVal value As String)
            _firstname = value
        End Set
    End Property
End Class

```
And then the Teacher class and ITeacher interface which inherits from IPerson.

```vbnet
Public Interface ITeacher
    Inherits IPerson
    Property ClassRoom() As String
End Interface

Public Class Teacher
    Inherits Person
    Private _classroom As String

    Public Sub New()

    End Sub

    Public Sub New(ByVal name As String, ByVal FirstName As String, ByVal ClassRoom As String)
        MyBase.New(Name, FirstName)
        _classroom = ClassRoom
    End Sub

    Public Property ClassRoom() As String
        Get
            Return _classroom
        End Get
        Set(ByVal value As String)
            _classroom = value
        End Set
    End Property
End Class
```
And then we bind it to the datagridview via a bindingsource. Like so

```vbnet
Public Class Form1
    Dim _bs As BindingSource

    Private Sub Form1_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
        Dim _l As New List(Of ITeacher)
        _l.Add(New Teacher("t1", "f1", "A"))
        _l.Add(New Teacher("t2", "f2", "B"))
        _l.Add(New Teacher("t3", "f3", "C"))
        _l.Add(New Teacher("t4", "f4", "D"))
        _l.Add(New Teacher("t5", "f5", "E"))
        _l.Add(New Teacher("t6", "f6", "F"))
        _l.Add(New Teacher("t7", "f7", "G"))
        _bs = New BindingSource
        _bs.DataSource = _l
        Me.DataGridView1.DataSource = _bs.DataSource
    End Sub
End Class
```
If we run this we will get this.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Datagridviewfrominterface.JPG" alt="" title="" width="778" height="297" />
</div>

So where did the person data go? 

And know watch even more closely and find the difference.

```vbnet
Public Class Form1
    Dim _bs As BindingSource

    Private Sub Form1_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
        Dim _l As New List(Of Teacher)
        _l.Add(New Teacher("t1", "f1", "A"))
        _l.Add(New Teacher("t2", "f2", "B"))
        _l.Add(New Teacher("t3", "f3", "C"))
        _l.Add(New Teacher("t4", "f4", "D"))
        _l.Add(New Teacher("t5", "f5", "E"))
        _l.Add(New Teacher("t6", "f6", "F"))
        _l.Add(New Teacher("t7", "f7", "G"))
        _bs = New BindingSource
        _bs.DataSource = _l
        Me.DataGridView1.DataSource = _bs.DataSource
    End Sub
End Class
```
And see the result.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/Datagridviewfromclass.JPG" alt="" title="" width="777" height="302" />
</div>

Yes the fields are there. Why does this happen? Well reflection and a bug in the interface implementation. Reflection should pick up the properties from IPerson in ITeacher like it does for the class but it doesn&#8217;t so your &#8230;&#8230;. The only solution that works is either bind to teacher or (like in my case) make a private class that accepts an ITeacher and has all the properties needed).

and why in my case? Because I only pass interface between my different layers, never implmentations.

Something like this.

```vbnet
Public Class LocalTeacher
    Implements ITeacher

    Private _teacher As ITeacher
    Public Sub New(ByVal Teacher As ITeacher)
        _teacher = Teacher
    End Sub

    Public Property Name() As String Implements IPerson.Name
        Get
            Return _teacher.Name
        End Get
        Set(ByVal value As String)
            _teacher.Name = value
        End Set
    End Property

    Public Property FirstName() As String Implements IPerson.FirstName
        Get
            Return _teacher.FirstName
        End Get
        Set(ByVal value As String)
            _teacher.FirstName = value
        End Set
    End Property


    Public Property ClassRoom() As String Implements ITeacher.ClassRoom
        Get
            Return _teacher.ClassRoom
        End Get
        Set(ByVal value As String)
            _teacher.ClassRoom = value
        End Set
    End Property

    Public ReadOnly Property Teacher() As ITeacher
        Get
            Return _teacher
        End Get
    End Property
End Class```
And then this for the form.

```vbnet
Public Class Form1
    Dim _bs As BindingSource

    Private Sub Form1_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
        'normally I would get this from the DAL
        Dim _l As New List(Of ITeacher)
        _l.Add(New Teacher("t1", "f1", "A"))
        _l.Add(New Teacher("t2", "f2", "B"))
        _l.Add(New Teacher("t3", "f3", "C"))
        _l.Add(New Teacher("t4", "f4", "D"))
        _l.Add(New Teacher("t5", "f5", "E"))
        _l.Add(New Teacher("t6", "f6", "F"))
        _l.Add(New Teacher("t7", "f7", "G"))
        'Then I have to do this to it
        Dim _l2 As New List(Of LocalTeacher)
        For Each T As ITeacher In _l
            _l2.Add(New LocalTeacher(T))
        Next
        _bs = New BindingSource
        _bs.DataSource = _l2
        Me.DataGridView1.DataSource = _bs.DataSource
    End Sub
End Class```
and then of course it&#8217;s a mess to save it again ;-). Call me nuts :crazy: and tell me I should just pass the objects. But trust me I won&#8217;t. I&#8217;ll keep the interfaces and live with this kind of thing.

<font color="Red">Need help with VB.Net? Come and ask a question in our <a href="http://forum.lessthandot.com/viewforum.php?f=39">VB.Net Forum</a></font>