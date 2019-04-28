---
title: Conditional formating in a DataGridView with databinding
author: Christiaan Baes (chrissie1)
type: post
date: 2010-03-11T11:18:01+00:00
ID: 723
url: /index.php/desktopdev/mstech/conditional-formating-in-a-datagridview/
views:
  - 10015
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - conditional formating
  - datagridview
  - vb.net

---
When you databind your DataGridView to a List.

Like this.

First I create a class Person so I can bind to that. Watch the default constructor, it needs to make new elements, if that is what you want.

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
Then the DataGridView.

```vbnet
''' &lt;summary&gt;
''' 
''' &lt;/summary&gt;
''' &lt;remarks&gt;&lt;/remarks&gt;
Public Class PersonsGrid
    Inherits DataGridView

#Region "Fields"

    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Private WithEvents _BindingSource As New BindingSource()
    Dim _persons As New List(Of Person)

#End Region 'Fields

#Region "Constructors"

    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Public Sub New()
        _persons.Add(New Person("Test1"))
        _persons.Add(New Person("Test2"))
        _persons.Add(New Person("Test3"))
        Me.BindToList()
    End Sub

#End Region 'Constructors

#Region "Methods" 'Methods

    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Public Sub BindToList()
        _BindingSource.DataSource = _persons
        Me.DataSource = _BindingSource
        _BindingSource.AllowNew = True
    End Sub

#End Region 'Methods
    
End Class
```
You can smack that onto a form to see the result.

Then you can conditionally format rows by using the DataBindingComplete event, like this.

```vbnet
Private Sub PersonsGrid_DataBindingComplete(ByVal sender As Object, ByVal e As System.Windows.Forms.DataGridViewBindingCompleteEventArgs) Handles Me.DataBindingComplete
            For Each row As DataGridViewRow In Me.Rows
                If Not row.IsNewRow Then
                    row.ReadOnly = True
                End If
            Next
        End Sub```
The above will make each row in the datagridview readonly except the new row (you know, the one with the * in front of it).

Or this.

```vbnet
''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;param name="sender"&gt;&lt;/param&gt;
    ''' &lt;param name="e"&gt;&lt;/param&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Private Sub PersonsGrid_DataBindingComplete(ByVal sender As Object, ByVal e As System.Windows.Forms.DataGridViewBindingCompleteEventArgs) Handles Me.DataBindingComplete
        For Each row As DataGridViewRow In Me.Rows
            If Not row.IsNewRow Then
                row.DefaultCellStyle.BackColor = Color.Red
            End If
        Next
    End Sub```
Which will make the backcolor of each cell go red except the new row one.

And I did that because it is visually appealing 😉

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Datagridview/Datagridview1.png" alt="" title="" width="300" height="300" />
</div>

Or this.

```vbnet
''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;param name="sender"&gt;&lt;/param&gt;
    ''' &lt;param name="e"&gt;&lt;/param&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    Private Sub PersonsGrid_DataBindingComplete(ByVal sender As Object, ByVal e As System.Windows.Forms.DataGridViewBindingCompleteEventArgs) Handles Me.DataBindingComplete
        For Each row As DataGridViewRow In Me.Rows
            If row.Cells(0).Value IsNot Nothing Then
                If Not row.Cells(0).Value.ToString = "Test2" Then
                    row.DefaultCellStyle.BackColor = Color.Red
                End If
            End If
        Next
    End Sub```
To make every row go red except the one with &#8220;Test2&#8221; in it.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Datagridview/Datagridview2.png" alt="" title="" width="300" height="300" />
</div>