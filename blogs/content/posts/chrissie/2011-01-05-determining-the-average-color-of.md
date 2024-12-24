---
title: Determining the average color of a fiber
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-05T09:19:00+00:00
ID: 995
excerpt: |
  Introduction
  
  So in part one we decided to use the AForge.Net framework to do this: Finding the fiber and determining the average color based on the image. And the saga continues.
  
  The average color
  
  After talking to Andrew Kirillov the developer&hellip;
url: /index.php/desktopdev/mstech/determining-the-average-color-of/
views:
  - 9044
categories:
  - Microsoft Technologies
  - VB.NET

---
## Introduction

So in [part one][1] we decided to use the AForge.Net framework to do this: Finding the fiber and determining the average color based on the image. And the saga continues.

## The average color

After talking to Andrew Kirillov the developer behind AForge.Net he told me about an easier way to do what I did.
  
And here it is.

```vbnet
Dim i = New Aforge.Imaging.Filters.ColorFiltering
		Dim b = GetBitmap()
		i.red = New intrange(150,255)
		i.Green = New intrange(150,255)
		i.blue = New intrange(150,255)
		i.FillOutsideRange = False
		Try
			PictureBox2.Image = i.Apply(b)
		Catch ex As Exception
			MessageBox.Show(ex.Message)
		End Try
		b = GetBitmap()
		Dim i2 = New AForge.Imaging.BlobCounter(b)
		Try
			Dim Biggestblob = i2.GetObjects(b,true).Where(Function(x) x.Area &gt; 1000).Reverse().FirstOrDefault
			PictureBox2.Image = i2.GetObjects(b,true).Where(Function(x) x.Area &gt; 1000)(0).Image
			Me.lblStatus.Text = "Mean: " & Biggestblob.ColorMean.ToString & " STD: " & Biggestblob.ColorStdDev.ToString
			Me.lblaveragecolor.BackColor = Biggestblob.ColorMean
			Me.HsbColorIndicator1.AverageColor = Biggestblob.ColorMean
			Me.lblstdcolor.BackColor = Biggestblob.ColorStdDev
		Catch ex As Exception
			MessageBox.Show(ex.Message)
		End Try```
This method is extremely fast. The result is the same.

Here is my version.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber2.png?mtime=1294139316"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber3.png?mtime=1294139316" width="753" height="544" /></a>
</div>

And here is the new method.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber3.png?mtime=1294139356"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber4.png?mtime=1294139356" width="753" height="544" /></a>
</div>

## Naming the color

And now that we have the average color of our fiber I want to name it. Since it is easier to talk about something when it has a name then when you have to site the RGB values. I thought about this mast night and while chatting with George Mastros he made me think about the HSB system and especially the H in that system. The H or hue makes it easy to separate colors. And it goes from 0 to 360. So I made a usercontrol to show the H values. Like this.

```vbnet
Imports System.Drawing.Drawing2D

Public Class HSBColorIndicator

	Private _AverageColor As Color 

	Public Sub New()
		InitializeComponent()
		Dim rect = New Rectangle(0, 0, 20, 360)
		Dim region = New Rectangle(0, -1, 18, 361)
		Dim m_colorSliderBitmap = New Bitmap(18, 360)
		Using g = Graphics.FromImage(m_colorSliderBitmap)
			Using brBrush = New LinearGradientBrush(rect, Color.Blue, Color.Red, 90.0F, False)
			Dim colorArray = {Color.Red, Color.Magenta, Color.Blue, Color.Cyan, Color.FromArgb(0, 255, 0), Color.Yellow, Color.Red}
			Dim posArray = {0.0F, 0.1667F, 0.3372F, 0.502F, 0.6686F, 0.8313F, 1.0F}

			Dim colorBlend = New ColorBlend()
			colorBlend.Colors = colorArray
			colorBlend.Positions = posArray
			brBrush.InterpolationColors = colorBlend

			g.FillRectangle(brBrush, region)
			g.DrawLine(Pens.Black, 0, 30, 18, 30)
			g.DrawLine(Pens.Black, 0, 90, 18, 90)
			g.DrawLine(Pens.Black, 0, 150, 18, 150)
			g.DrawLine(Pens.Black, 0, 210, 18, 210)
			g.DrawLine(Pens.Black, 0, 270, 18, 270)
			g.DrawLine(Pens.Black, 0, 330, 18, 330)
			End Using
		End Using
		Me.hsbcolorbar.Image = m_colorSliderBitmap
	End Sub

	Public Property AverageColor As Color
		Get
			Return _AverageColor
		End Get
		Set(ByVal value As Color)
			_AverageColor = value
			DrawArrow
		End Set
	End Property

	Private Sub DrawArrow()
		Me.Invalidate(New Rectangle(19, 0, 200, 360))
		Me.Refresh()
		Dim x = 19
		Dim y = Convert.ToInt32(360 - _AverageColor.GetHue)
		Using g = Me.CreateGraphics
			Dim points = {New Point(x, y), New Point(x + 10, y - 3), New Point(x + 10, y + 3)}
			g.DrawPolygon(Pens.White, points)
			g.DrawString(GetColorName, New Font(New FontFamily("Arial"), 7), Brushes.White, 31, y - 5)
		End Using
	End Sub

	Private Function GetColorName() As String
		Dim _Color As String = "Not defined"
		Dim hue = 360 - _AverageColor.GetHue
		If hue &gt; 30 AndAlso hue &lt;= 90 Then
			_Color = "Violet"
		ElseIf hue &gt; 90 AndAlso hue &lt;= 150 Then
			_Color = "Blue"
		ElseIf hue &gt; 150 AndAlso hue &lt;= 210 Then
			_Color = "Turquoise"
		ElseIf hue &gt; 210 AndAlso hue &lt;= 270 Then
			_Color = "Green"
		ElseIf hue &gt; 270 AndAlso hue &lt;= 330 Then
			_Color = "Yellow"
		Else
			_Color = "Red"
		End If
		Return _Color
	End Function
End Class
```
So I decided on some boundaries and I show an arrow where the average color is situated within the hue range, simple. Getting the hue from a color in .Net is very easy since you can just do GetHue(). 

And here is the result of the Belgian jury.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber5.png?mtime=1294226022"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber5.png?mtime=1294226022" width="831" height="511" /></a>
</div>

And I&#8217;m actually quit pleased with the result.

## Conclusion

Working with colors is not that hard but you need to know about a lot of things to make it easy and manageable. Every color system has it uses and it&#8217;s advantages and disadvantages.

 [1]: /index.php?p=1064