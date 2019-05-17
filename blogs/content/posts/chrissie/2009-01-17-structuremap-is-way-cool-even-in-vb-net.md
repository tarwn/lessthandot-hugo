---
title: StructureMap is way cool even in VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-17T07:39:02+00:00
ID: 288
url: /index.php/desktopdev/mstech/structuremap-is-way-cool-even-in-vb-net/
views:
  - 4685
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - buildup
  - structuremap
  - vb.net

---
In [my previous post][1] I already mentioned the BuildUp method in StructureMap and how cool that can be. And now I want to show you how to do this in VB.Net. As we all know VB.Net doesn&#8217;t like Multiline lambdas or void lambdas. But we will kill somebody for that later. In this example I also used a real form and it didn&#8217;t get hurt by the designer this time. 

The code is pretty much the same as my previous post. 

Here are the services and their interfaces.

```vbnet
Namespace Services
    Public Interface IService1
        Property Test() As String
    End Interface
End Namespace```
```vbnet
Namespace Services
    Public Interface IService2
        Property Test() As String
    End Interface
End Namespace
```
```vbnet
Namespace Services
    Public Class Service1
        Implements IService1

        Private _Test As String = "Service1"

        Public Property Test() As String Implements IService1.Test
            Get
                Return _Test
            End Get
            Set(ByVal value As String)
                _Test = value
            End Set
        End Property
    End Class
End Namespace
```
```vbnet
Namespace Services
    Public Class Service2
        Implements IService2

        Private _Test As String = "Service2"

        Public Property Test() As String Implements IService2.Test
            Get
                Return _Test
            End Get
            Set(ByVal value As String)
                _Test = value
            End Set
        End Property
    End Class
End Namespace```
And here is the usercontrols with the basecontrol and the buildup in there.

```vbnet
Imports System.Windows.Forms

Namespace View.Controls
    Public Class BaseControl
        Inherits TextBox

        Public Sub New()
            StructureMap.ObjectFactory.BuildUp(Me)
        End Sub
    End Class
End Namespace
```
```vbnet
Namespace View.Controls

    Public Class UserControl1
        Inherits Basecontrol

        Private _Service As Services.IService1

        Public Sub New()
            MyBase.New()
            If Service IsNot Nothing Then
                Me.Text = Service.Test
            End If
        End Sub

        Public Property Service() As Services.IService1
            Get
                Return _Service
            End Get
            Set(ByVal value As Services.IService1)
                _Service = value
            End Set
        End Property
    End Class
End Namespace```
```vbnet
Namespace View.Controls

    Public Class UserControl2
        Inherits Basecontrol

        Private _Service As Services.IService2

        Public Sub New()
            MyBase.New()
            If Service IsNot Nothing Then
                Me.Text = Service.Test
            End If
        End Sub

        Public Property Service() As Services.IService2
            Get
                Return _Service
            End Get
            Set(ByVal value As Services.IService2)
                _Service = value
            End Set
        End Property
    End Class
End Namespace```
And the interface for the Form.

```vbnet
Namespace View.Form
    Public Interface IForm
        Sub ShowDialog()
    End Interface
End Namespace
```
And here is the codebehind of the form.

```vbnet
Namespace View.Form
    Public Class Form1
        Implements IForm

        Public Sub ShowDialog1() Implements IForm.ShowDialog
            Me.ShowDialog()
        End Sub
    End Class
End Namespace```
And the designer of the form.

```vbnet
Namespace View.Form
    &lt;Global.Microsoft.VisualBasic.CompilerServices.DesignerGenerated()&gt; _
    Partial Class Form1
        Inherits System.Windows.Forms.Form

        'Form overrides dispose to clean up the component list.
        &lt;System.Diagnostics.DebuggerNonUserCode()&gt; _
        Protected Overrides Sub Dispose(ByVal disposing As Boolean)
            Try
                If disposing AndAlso components IsNot Nothing Then
                    components.Dispose()
                End If
            Finally
                MyBase.Dispose(disposing)
            End Try
        End Sub

        'Required by the Windows Form Designer
        Private components As System.ComponentModel.IContainer

        'NOTE: The following procedure is required by the Windows Form Designer
        'It can be modified using the Windows Form Designer.  
        'Do not modify it using the code editor.
        &lt;System.Diagnostics.DebuggerStepThrough()&gt; _
        Private Sub InitializeComponent()
            Me.UserControl11 = New Structuremaptrialvb.net.View.Controls.UserControl1
            Me.UserControl21 = New Structuremaptrialvb.net.View.Controls.UserControl2
            Me.Label1 = New System.Windows.Forms.Label
            Me.Label2 = New System.Windows.Forms.Label
            Me.SuspendLayout()
            '
            'UserControl11
            '
            Me.UserControl11.Location = New System.Drawing.Point(138, 60)
            Me.UserControl11.Name = "UserControl11"
            Me.UserControl11.Service = Nothing
            Me.UserControl11.Size = New System.Drawing.Size(100, 20)
            Me.UserControl11.TabIndex = 0
            '
            'UserControl21
            '
            Me.UserControl21.Location = New System.Drawing.Point(138, 106)
            Me.UserControl21.Name = "UserControl21"
            Me.UserControl21.Service = Nothing
            Me.UserControl21.Size = New System.Drawing.Size(100, 20)
            Me.UserControl21.TabIndex = 1
            '
            'Label1
            '
            Me.Label1.AutoSize = True
            Me.Label1.Location = New System.Drawing.Point(27, 60)
            Me.Label1.Name = "Label1"
            Me.Label1.Size = New System.Drawing.Size(39, 13)
            Me.Label1.TabIndex = 2
            Me.Label1.Text = "Label1"
            '
            'Label2
            '
            Me.Label2.AutoSize = True
            Me.Label2.Location = New System.Drawing.Point(31, 104)
            Me.Label2.Name = "Label2"
            Me.Label2.Size = New System.Drawing.Size(39, 13)
            Me.Label2.TabIndex = 3
            Me.Label2.Text = "Label2"
            '
            'Form1
            '
            Me.AutoScaleDimensions = New System.Drawing.SizeF(6.0!, 13.0!)
            Me.AutoScaleMode = System.Windows.Forms.AutoScaleMode.Font
            Me.ClientSize = New System.Drawing.Size(286, 267)
            Me.Controls.Add(Me.Label2)
            Me.Controls.Add(Me.Label1)
            Me.Controls.Add(Me.UserControl21)
            Me.Controls.Add(Me.UserControl11)
            Me.Name = "Form1"
            Me.Text = "Form1"
            Me.ResumeLayout(False)
            Me.PerformLayout()

        End Sub
        Friend WithEvents UserControl11 As Structuremaptrialvb.net.View.Controls.UserControl1
        Friend WithEvents UserControl21 As Structuremaptrialvb.net.View.Controls.UserControl2
        Friend WithEvents Label1 As System.Windows.Forms.Label
        Friend WithEvents Label2 As System.Windows.Forms.Label
    End Class
End Namespace
```
We also need to configure structuremap.

```vbnet
Namespace IoC
    Public Class Configure
        Public Sub Setup()
            StructureMap.ObjectFactory.Initialize(Function(x) InitializeStructureMap(x))
        End Sub

        Private Function InitializeStructureMap(ByRef x As StructureMap.IInitializationExpression) As Boolean
            x.ForRequestedType(Of Services.IService1).TheDefault.Is.OfConcreteType(Of Services.Service1)()
            x.ForRequestedType(Of Services.IService2).TheDefault.Is.OfConcreteType(Of Services.Service2)()
            x.ForRequestedType(Of View.Form.IForm).TheDefault.Is.OfConcreteType(Of View.Form.Form1)()
            x.SetAllProperties(Function(y) SetTheProperties(y))
            Return True
        End Function

        Private Function SetTheProperties(ByVal y As StructureMap.Configuration.DSL.SetterConvention) As Boolean
            y.OfType(Of Services.IService1)()
            y.OfType(Of Services.IService2)()
            Return True
        End Function
    End Class
End Namespace
```
Not painfull but not pretty either. And exactly the same as the C# thing.

And finaly the Module to run the stuff.

```vbnet
Option Infer On

Imports StructureMap

Module Module1
    Public Sub Main()
        Dim _IoC = New IoC.Configure()
        _IoC.Setup()
        Dim _Form = ObjectFactory.GetInstance(Of View.Form.IForm)()
        _Form.ShowDialog()
    End Sub
End Module
```
The form now looks like this when run.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Structuremap/structuremapbuildupform2.jpg" alt="" title="" width="304" height="300" />
</div>

And as you can see I can even open it in the designer.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Structuremap/structuremapbuildupform.jpg" alt="" title="" width="746" height="545" />
</div>

And no instatiating the service anywhere. and only one ObjectFactory.GetInstance in the whole project.

Live is wonderfull.

I also have the solution here. StructureMap sourcecode included, it&#8217;s the one from the trunk.

[Structuremaptrial.zip][2] 

Sorry for this extra long post.

**<span class="MT_red">You can always ask question about this in our <a href="http://forum.lessthandot.com/viewforum.php?f=39">VB.Net forum</a>.</span>**

 [1]: /index.php/DesktopDev/MSTech/structuremap-is-way-cool
 [2]: /wp-content/uploads/blogs/DesktopDev/Structuremap/Structuremaptrial.zip "Structuremaptrial.zip"