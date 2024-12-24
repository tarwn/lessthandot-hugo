---
title: 'VB.Net: drawing a string on a panel that is created at runtime.'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-11-28T11:29:29+00:00
ID: 223
url: /index.php/desktopdev/mstech/vb-net-drawing-a-string-on-a-panel-that/
views:
  - 15705
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - drawstring
  - panel
  - vb.net

---
Today I had to draw a text on a panel that I had created at runtime. This is actually pretty easy but there are some pitfalls that can take a few minutes to figure out. This is all still in the good old windows forms, none of that WPF magic for me ;-).

So first you create a form.

And then in the load event of that form we create this.

```vbnet
Private Sub Form1_Load(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles MyBase.Load
        _Panel = New Panel
        _Panel.Dock = DockStyle.Fill
        _Panel.BackColor = Color.LightCyan
        Dim _graphics As Graphics = _Panel.CreateGraphics
        _graphics.DrawString("The brown fox thing.", New Font("Arial", 12, FontStyle.Regular, GraphicsUnit.Point, Nothing), New System.Drawing.SolidBrush(Color.Gray), Convert.ToInt32(Me.Width / 2), Convert.ToInt32(Me.Height / 2))
        _Panel.Visible = True
        Me.Controls.Add(_Panel)
    End Sub```
Result

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/panel1.jpg" alt="" title="" width="301" height="301" />
</div>

Weird no text. Well, I can explain that. If you look very carefully and change your code to this:

```vbnet
Private Sub Form1_Load(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles MyBase.Load
        _Panel = New Panel
        _Panel.Dock = DockStyle.Fill
        _Panel.BackColor = Color.LightCyan
        _Panel.Visible = True
        Me.Controls.Add(_Panel)
Dim _graphics As Graphics = _Panel.CreateGraphics
        _graphics.DrawString("The brown fox thing.", New Font("Arial", 12, FontStyle.Regular, GraphicsUnit.Point, Nothing), New System.Drawing.SolidBrush(Color.Gray), Convert.ToInt32(Me.Width / 2), Convert.ToInt32(Me.Height / 2))
    End Sub```
Then you will see whether the text to be drawn quickly disappears or not ;-). What happens is that after you draw the text on the graphics object, the panel is redrawn. The paint event of this panel doesn&#8217;t know about the drawstring and doesn&#8217;t, therefore, draw it again.

The solution is to draw the string in the paint event of the panel.

Which looks like this:

```vbnet
Private _Panel As Panel

    Private Sub Form1_Load(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles MyBase.Load
        _Panel = New Panel
        _Panel.Dock = DockStyle.Fill
        _Panel.BackColor = Color.LightCyan
        _Panel.Visible = True
        Me.Controls.Add(_Panel)
        AddHandler _Panel.Paint, AddressOf DrawText
    End Sub

    Private Sub DrawText(ByVal obj As Object, ByVal e As PaintEventArgs)
        e.Graphics.DrawString("Rightclick to add or Delete an image.", New Font("Arial", 12, FontStyle.Regular, GraphicsUnit.Point, Nothing), New System.Drawing.SolidBrush(Color.Gray), Convert.ToInt32(Me.Width / 2), Convert.ToInt32(Me.Height / 2), string_format)
    End Sub```
And this gives this result.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/panel2.jpg" alt="" title="" width="301" height="301" />
</div>

The text isn&#8217;t all that beautifully placed but we can do something about that.

Change your code to this:

```vbnet
Public Class Form1
    Private _Panel As Panel

    Private Sub Form1_Load(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles MyBase.Load
        _Panel = New Panel
        _Panel.Dock = DockStyle.Fill
        _Panel.BackColor = Color.LightCyan
        _Panel.Visible = True
        Me.Controls.Add(_Panel)
        AddHandler _Panel.Paint, AddressOf DrawText
    End Sub

    Private Sub DrawText(ByVal obj As Object, ByVal e As PaintEventArgs)
        Using string_format As New StringFormat()
            string_format.Alignment = StringAlignment.Center
            string_format.LineAlignment = StringAlignment.Center
           e.Graphics.DrawString("Rightclick to add or Delete an image.", New Font("Arial", 12, FontStyle.Regular, GraphicsUnit.Point, Nothing), New System.Drawing.SolidBrush(Color.Gray), Convert.ToInt32(Me.ClientSize.Width / 2), Convert.ToInt32(Me.ClientSize.Height / 2), string_format)
        End Using
    End Sub
End Class
```
This is the result:

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/panel3.jpg" alt="" title="" width="301" height="301" />
</div>

Now the text is nicely centered without having to do to much tedious calculation. The stringformat does most of the hard work for us.

You can find more on this site.

[Master .NET&#8217;s Text Tricks][1]

 [1]: http://www.devx.com/dotnet/Article/33464/1954