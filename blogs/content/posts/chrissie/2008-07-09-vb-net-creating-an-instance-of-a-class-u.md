---
title: 'VB.Net: creating an instance of a class using reflection'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-07-09T10:35:10+00:00
ID: 82
url: /index.php/desktopdev/mstech/vb-net-creating-an-instance-of-a-class-u/
views:
  - 16230
categories:
  - Microsoft Technologies

---
It can be usefull to be able to create an instance of a class via reflection. I wouldn&#8217;t use it to much because it has a code smell. But anyway. 

I wrote this little windowsapplication that creates some controls.

This is the code in that form.

```vbnet
Imports System.Reflection

Public Class Form3

    Dim controlsList As List(Of Control)

    Private Sub Button1_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button1.Click
        If Not String.IsNullOrEmpty(Me.ComboBox1.Text) Then
            Dim asm As [Assembly]

            '*** get assembly from WordAssist.com
            asm = [Assembly].GetAssembly(GetType(System.Windows.Forms.Control))

            '*** create object instance by having full class name
            Dim obj As Control = asm.CreateInstance("System.Windows.Forms." & Me.ComboBox1.Text, True)
            obj.Top = 20 * controlsList.Count
            obj.Left = 20
            obj.Height = 20
            obj.Width = 100
            obj.Visible = True
            obj.Name = Me.ComboBox1.Text & controlsList.Count
            obj.Text = obj.Name
            controlsList.Add(obj)
            Me.SplitContainer1.Panel2.Controls.Add(obj)
        End If
    End Sub

    Private Sub Form3_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
        controlsList = New List(Of Control)
    End Sub
End Class```
I used a form and this is the designer code for that.

```vbnet
&lt;Global.Microsoft.VisualBasic.CompilerServices.DesignerGenerated()&gt; _
Partial Class Form3
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
        Me.ComboBox1 = New System.Windows.Forms.ComboBox
        Me.Label1 = New System.Windows.Forms.Label
        Me.Button1 = New System.Windows.Forms.Button
        Me.SplitContainer1 = New System.Windows.Forms.SplitContainer
        Me.SplitContainer1.Panel1.SuspendLayout()
        Me.SplitContainer1.SuspendLayout()
        Me.SuspendLayout()
        '
        'ComboBox1
        '
        Me.ComboBox1.FormattingEnabled = True
        Me.ComboBox1.Items.AddRange(New Object() {"TextBox", "ComboBox", "ListBox", "Label"})
        Me.ComboBox1.Location = New System.Drawing.Point(61, 3)
        Me.ComboBox1.Name = "ComboBox1"
        Me.ComboBox1.Size = New System.Drawing.Size(121, 21)
        Me.ComboBox1.TabIndex = 0
        '
        'Label1
        '
        Me.Label1.AutoSize = True
        Me.Label1.Location = New System.Drawing.Point(3, 3)
        Me.Label1.Name = "Label1"
        Me.Label1.Size = New System.Drawing.Size(40, 13)
        Me.Label1.TabIndex = 1
        Me.Label1.Text = "Control"
        '
        'Button1
        '
        Me.Button1.Location = New System.Drawing.Point(198, 3)
        Me.Button1.Name = "Button1"
        Me.Button1.Size = New System.Drawing.Size(75, 23)
        Me.Button1.TabIndex = 2
        Me.Button1.Text = "create"
        Me.Button1.UseVisualStyleBackColor = True
        '
        'SplitContainer1
        '
        Me.SplitContainer1.BorderStyle = System.Windows.Forms.BorderStyle.FixedSingle
        Me.SplitContainer1.Dock = System.Windows.Forms.DockStyle.Fill
        Me.SplitContainer1.Location = New System.Drawing.Point(0, 0)
        Me.SplitContainer1.Name = "SplitContainer1"
        Me.SplitContainer1.Orientation = System.Windows.Forms.Orientation.Horizontal
        '
        'SplitContainer1.Panel1
        '
        Me.SplitContainer1.Panel1.Controls.Add(Me.Label1)
        Me.SplitContainer1.Panel1.Controls.Add(Me.Button1)
        Me.SplitContainer1.Panel1.Controls.Add(Me.ComboBox1)
        Me.SplitContainer1.Size = New System.Drawing.Size(639, 414)
        Me.SplitContainer1.SplitterDistance = 46
        Me.SplitContainer1.TabIndex = 3
        '
        'Form3
        '
        Me.AutoScaleDimensions = New System.Drawing.SizeF(6.0!, 13.0!)
        Me.AutoScaleMode = System.Windows.Forms.AutoScaleMode.Font
        Me.ClientSize = New System.Drawing.Size(639, 414)
        Me.Controls.Add(Me.SplitContainer1)
        Me.Name = "Form3"
        Me.Text = "Form3"
        Me.SplitContainer1.Panel1.ResumeLayout(False)
        Me.SplitContainer1.Panel1.PerformLayout()
        Me.SplitContainer1.ResumeLayout(False)
        Me.ResumeLayout(False)

    End Sub
    Friend WithEvents ComboBox1 As System.Windows.Forms.ComboBox
    Friend WithEvents Label1 As System.Windows.Forms.Label
    Friend WithEvents Button1 As System.Windows.Forms.Button
    Friend WithEvents SplitContainer1 As System.Windows.Forms.SplitContainer
End Class
```