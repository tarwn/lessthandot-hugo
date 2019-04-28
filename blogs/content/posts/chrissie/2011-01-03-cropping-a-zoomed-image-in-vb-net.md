---
title: Cropping a zoomed image in VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-03T07:44:00+00:00
ID: 968
excerpt: |
  Introduction
  
  Cropping an Image is pretty simple in VB.Net.
  
  Today I had a need to make it possible for my users to crop some images by drawing a selection rectangle and I felt the need to reinvent the wheel ;-).
  
  Not zoomed image
  
  Cropping isn'&hellip;
url: /index.php/desktopdev/mstech/cropping-a-zoomed-image-in-vb-net/
views:
  - 6743
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - crop image
  - vb.net

---
## Introduction

Cropping an Image is pretty simple in VB.Net.

Today I had a need to make it possible for my users to crop some images by drawing a selection rectangle and I felt the need to reinvent the wheel ;-).

## Not zoomed image

Cropping isn&#8217;t all that difficult with VB. As you can see from the following piece of code.

```vbnet
Dim bmp As New Bitmap(newwidth,newheight)              
Dim g = Graphics.FromImage(bmp)
g.DrawImage(PictureBox1.Image,0,0, New Rectangle(newtop, newleft, newwidth, newheight), GraphicsUnit.Pixel)```<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/croppedimage/CroppedImage1.png?mtime=1294047503"><img alt="" src="/wp-content/uploads/users/chrissie1/croppedimage/CroppedImage1.png?mtime=1294047503" width="860" height="556" /></a>
</div>

First thing to do was show the image, since these are scanned pages from books they are going to be a rather large format. So the picturebox will have to be able to zoom to show the image. Now this is where it gets interesting. But I did ;-). The biggest problem was to detemine the zoomfactor used in the original so that the cutout would match. Perhaps I&#8217;m doing it all wrong but it works.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/croppedimage/CroppedImage.png?mtime=1294047492"><img alt="" src="/wp-content/uploads/users/chrissie1/croppedimage/CroppedImage.png?mtime=1294047492" width="860" height="556" /></a>
</div>

And here is the not yet perfect code.

```vb.net
Imports System.Drawing.Drawing2D
     
   Public Partial Class MainForm
       
        Private _CropRectangle As CropRectangle
        Private _ZoomFactor As Double
     
        Public Sub New()
      
           Me.InitializeComponent()
           _CropRectangle = New CropRectangle
    
           If pictureBox1.Image.Width &gt; pictureBox1.Image.Height Then
               _ZoomFactor = Me.Panel1.Width / pictureBox1.Image.Width * 100
           Else
               _ZoomFactor = Me.Panel1.Height / pictureBox1.Image.Height * 100
           End If
           If (pictureBox1.Image.Height / 100 * _ZoomFactor) &gt; (Me.Panel1.Height) Then
               _ZoomFactor = (Me.Panel1.Height) / pictureBox1.Image.Height * 100
           End If
           If (pictureBox1.Image.Width / 100 * _ZoomFactor) &gt; Me.Panel1.Width Then
               _ZoomFactor = Me.Panel1.Width / pictureBox1.Image.Width * 100
           End If
           If _ZoomFactor &lt; 0.2 Then
               _ZoomFactor = 0.2
           End If
           If _ZoomFactor &gt; 100 Then
               _ZoomFactor = 100
           End If
           Dim _Height As Integer
           _Height = Convert.ToInt32 (Me.PictureBox1.image.Height/100*_ZoomFactor)
           Dim _Width As Integer
           _Width = Convert.ToInt32 (Me.PictureBox1.image.Width/100*_ZoomFactor)
           Dim _Left As Integer
           If _Width &lt; Me.Panel1.Width Then
               _Left = Convert.ToInt32 ((Me.Panel1.Width - _Width)/2)
           Else
               _Left = 0
           End If
           Dim _Top As Integer
           If _Height &lt; (Me.Panel1.Height) Then
               _Top = Convert.ToInt32 ((Me.Panel1.Height - _Height)/2)
           Else
               _Top = 0
           End If
           _CropRectangle.ZoomFactor = _ZoomFactor
           setImage(True, _Height, _Width, _Left, _Top)
       End Sub
      
       private Sub SetImage(ByVal Visible As Boolean, ByVal Height As Integer, ByVal Width As Integer, ByVal Left As Integer, ByVal Top As Integer)
           PictureBox1.Visible = Visible
           PictureBox1.Height = Height
           PictureBox1.Width = Width
           PictureBox1.Left = Left
           PictureBox1.Top = Top
       End Sub
    
       Private Sub PictureBox1_MouseDown( ByVal sender As System.Object,  ByVal e As System.Windows.Forms.MouseEventArgs) Handles PictureBox1.MouseDown
           _CropRectangle.CropX = e.X
           _CropRectangle.CropY = e.Y
           _CropRectangle.CropX2 = e.X
           _CropRectangle.CropY2 = e.Y
           _CropRectangle.Initialized = True
       End Sub
    
       Private Sub PictureBox1_MouseMove(ByVal sender As Object, ByVal e As System.Windows.Forms.MouseEventArgs) Handles PictureBox1.MouseMove
           If _CropRectangle.Initialized = True
               _CropRectangle.CropX2 = e.X
               _CropRectangle.CropY2 = e.Y
               _CropRectangle.Draw(Me.PictureBox1)
           end if
       End Sub
    
       Private Sub PictureBox1_MouseUp(ByVal sender As Object, ByVal e As System.Windows.Forms.MouseEventArgs) Handles PictureBox1.MouseUp
           _CropRectangle.Initialized = False
           _CropRectangle.DrawCroppedImage(PictureBox1, PictureBox2)
       End Sub
    
    
       Private Class CropRectangle
           Private _CropPen As Pen
    
           Public Sub New
               _cropPen = New Pen(Color.green, 1)    
               _cropPen.DashStyle = DashStyle.Dot      
           End Sub
    
           Public Property Initialized As Boolean = False
           Public Property CropX As Integer
           Public Property CropY As Integer
           Public Property CropX2 As Integer
           Public Property CropY2 As Integer
           Public Property ZoomFactor As Double
    
           Public readonly Property CropPen As Pen
               Get
                   Return _CropPen
               End Get
          End Property
   
          Public sub DrawCroppedImage(ByVal PictureBox1 As PictureBox, Byref PictureBox2 As PictureBox)
              Dim bmp As New Bitmap(Convert.ToInt32((_CropX2 - _CropX)/ZoomFactor*100), Convert.ToInt32((_CropY2 - _CropY)/ZoomFactor*100))              
              Dim g = Graphics.FromImage(bmp)
              g.DrawImage(PictureBox1.Image,0,0, New Rectangle(_cropx/ZoomFactor*100, _cropy/ZoomFactor*100, (_cropx2 -_cropx)/ZoomFactor*100, (CropY2 - _cropy)/ZoomFactor*100), GraphicsUnit.Pixel)
              PictureBox2.Image = bmp
          End Sub
   
          Public sub Draw(ByRef PictureBox1 As PictureBox)
              PictureBox1.Refresh()
              Dim g = PictureBox1.CreateGraphics
              g.DrawRectangle(_cropPen,_cropx, _cropy, _cropx2 -_cropx, CropY2 - _cropy)
          End Sub
         
      End Class
         
  End Class
```
Here is the downloadable code in VS 2010 format.

[CroppedImage.zip][1]

 [1]: /wp-content/uploads/users/chrissie1/croppedimage/CroppedImage.zip?mtime=1294047694