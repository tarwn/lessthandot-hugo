---
title: Improving the code for determining the average color of a fiber
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-07T13:19:00+00:00
ID: 998
excerpt: |
  Introduction
  
  In my last blog I was playing with the AForge.Net framework and trying to get it to work. This resulted in less than optimal code and no tests what so ever. I find it is nearly impossible to write tests for something you don't even know&hellip;
url: /index.php/desktopdev/mstech/improving-the-code-for-determining/
views:
  - 5141
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - aforge.net
  - vb.net

---
## Introduction

In my last blog I was playing with the AForge.Net framework and trying to get it to work. This resulted in less than optimal code and no tests what so ever. I find it is nearly impossible to write tests for something you don&#8217;t even know what you want it to do. So today I sat behind my PC and improved the code a bit. So that my colleagues can now test this and see if they like it or have any comments on it. The step after that is to incorporate it into the application and to make it possible to search in our database for fibers with a similar hue.

This is the finished result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber6.png?mtime=1294411922"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber6.png?mtime=1294411922" width="866" height="734" /></a>
</div>

## Separating

To begin with everything was in my view. I don&#8217;t mind using the codebehind but it should only be for view things in my mind. So I think it needed a Facade/ViewModel to put things in it that do not belong in the view.

Here is the interface I came up with.

```vbnet
Namespace FiberColor.View.Facade.Interfaces
	Public Interface IFiberColorFacade
		Event BlobsCountChanged(ByVal Count As Integer)
		Event CurrentBlobChanged(ByVal Index As Integer, ByVal Blob As Blob)
		Sub FindBlobs(ByVal Image As System.Drawing.Image)
		Sub FirstBlob()
		Sub NextBlob()
		Sub PreviousBlob()
	End Interface
End Namespace```
And this is the implementation.

```vbnet
Option Infer On

Imports System.Drawing
Imports AForge
Imports AForge.Imaging
Imports TDB2009.View.Winforms.FiberColor.View.Facade.Interfaces

Namespace FiberColor.View.Facade
	Public Class FiberColorFacade
		Implements IFiberColorFacade
		
		Private _Blobs() As Blob
		Private _BlobCounter As BlobCounter
		Private _CurrentImage As Bitmap
		Private _Index As Integer

		Public Event BlobsCountChanged(ByVal Count As Integer) Implements Interfaces.IFiberColorFacade.BlobsCountChanged

		Public Event CurrentBlobChanged(ByVal Index As Integer, ByVal Blob As AForge.Imaging.Blob) Implements Interfaces.IFiberColorFacade.CurrentBlobChanged

		Private Sub ChangeCurrentBlob()
			If _Blobs.Count &gt; 0 Then
				_BlobCounter.ExtractBlobsImage(_CurrentImage, _Blobs(_Index), True)
				RaiseEvent CurrentBlobChanged(_Index, _Blobs(_Index))
			Else
				RaiseEvent CurrentBlobChanged(_Index, Nothing)
			End If
		End Sub

		Public Sub FindBlobs(ByVal Image As System.Drawing.Image) Implements Interfaces.IFiberColorFacade.FindBlobs
			Dim _ColorFilter = New AForge.Imaging.Filters.ColorFiltering
			_CurrentImage = New Bitmap(Image.Width, Image.Height, System.Drawing.Imaging.PixelFormat.Format24bppRgb)
			Using _Graphics = Graphics.FromImage(_CurrentImage)
				_Graphics.DrawImage(Image, 0, 0, New Rectangle(0, 0, Image.Width, Image.Height), GraphicsUnit.Pixel)
			End Using
			_ColorFilter.Red = New IntRange(150, 255)
			_ColorFilter.Green = New IntRange(150, 255)
			_ColorFilter.Blue = New IntRange(150, 255)
			_ColorFilter.FillOutsideRange = False
			_ColorFilter.ApplyInPlace(_CurrentImage)
			_BlobCounter = New AForge.Imaging.BlobCounter(_CurrentImage)
			_Blobs = (From e In _BlobCounter.GetObjectsInformation Where e.Area &gt; 20000 Order By e.Area Descending Select e).ToArray
			FirstBlob()
			RaiseEvent BlobsCountChanged(_Blobs.Count)
		End Sub

		Public Sub FirstBlob() Implements Interfaces.IFiberColorFacade.FirstBlob
			_Index = 0
			ChangeCurrentBlob()
		End Sub

		Public Sub NextBlob() Implements Interfaces.IFiberColorFacade.NextBlob
			If _Index &lt; _Blobs.Count - 1 Then
				_Index += 1
			End If
			ChangeCurrentBlob()
		End Sub

		Public Sub PreviousBlob() Implements Interfaces.IFiberColorFacade.PreviousBlob
			If _Index &gt; 0 Then
				_Index -= 1
			End If
			ChangeCurrentBlob()
		End Sub
	End Class
End Namespace```
You see that all references to AForge.Net are now in here. There should not be any in the view. I go by the principle Tell don&#8217;t ask. I tell the facade to do something and it raises an event that tells the view it can update. What remains is the view.

## The view

The view has a reference to the Facade. And reacts to the events of our facade.

```vbnet
Imports System.Drawing
Imports TDB2009.View.Winforms.FiberColor.View.Facade.Interfaces
Imports TDB2009.View.Interfaces.FiberColor.Forms

Namespace FiberColor.View.Forms
	''' &lt;summary&gt;
	''' 
	''' &lt;/summary&gt;
	''' &lt;remarks&gt;&lt;/remarks&gt;
	Public Class FiberColor
		Implements IFiberColor

		Private WithEvents _Facade As IFiberColorFacade

		Public Sub New(ByVal Facade As IFiberColorFacade)

			' This call is required by the designer.
			InitializeComponent()

			' Add any initialization after the InitializeComponent() call.
			_Facade = Facade
		End Sub

		Private Sub mnuOpen_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles mnuOpen.Click
			OpenFileDialog1.Filter = "Jpeg files(*.jpg)|*.jpg"
			If OpenFileDialog1.ShowDialog = Windows.Forms.DialogResult.OK Then
				Me.lblFilename.Text = OpenFileDialog1.FileName
				OriginalImage.Image = Image.FromFile(OpenFileDialog1.FileName, False)
				_Facade.FindBlobs(OriginalImage.Image)
			End If
		End Sub

		Private Sub mnuExit_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles mnuExit.Click
			Me.Close()
		End Sub

		Private Sub _Facade_BlobsCountChanged(ByVal Count As Integer) Handles _Facade.BlobsCountChanged
			If Count &gt; 0 Then
				panMoreImages.Visible = True
				lblCount.Text = "of " & Count
			Else
				panMoreImages.Visible = False
				lblStatus.Text = "No images found"
			End If
		End Sub

		Private Sub _Facade_CurrentBlobChanged(ByVal Index As Integer, ByVal Blob As AForge.Imaging.Blob) Handles _Facade.CurrentBlobChanged
			If Blob IsNot Nothing Then
				Me.lblStatus.Text = "Mean: (R = " & Blob.ColorMean.R & " - G = " & Blob.ColorMean.G & " - B = " & Blob.ColorMean.B & ") STD: (R = " & Blob.ColorStdDev.R & " - G = " & Blob.ColorStdDev.G & " - B = " & Blob.ColorStdDev.B & ") Surface: " & Blob.Area
				Me.panMean.BackColor = Blob.ColorMean
				Me.HsbColorIndicator1.AverageColor = Blob.ColorMean
				Me.panStandarddeviation.BackColor = Blob.ColorStdDev
				FoundFiber.Image = Blob.Image
				lblNumber.Text = (Index + 1).ToString
			Else
				Me.HsbColorIndicator1.AverageColor = Color.Transparent
				Me.lblStatus.Text = "No Blob found"
				Me.panMean.BackColor = Color.Transparent
				Me.panStandarddeviation.BackColor = Color.Transparent
				FoundFiber.Image = Nothing
			End If
		End Sub

		Private Sub btnNextImage_Click(ByVal sender As Object, ByVal e As System.EventArgs) Handles btnNextImage.Click
			_Facade.NextBlob()
		End Sub

		Private Sub btnPreviousImage_Click(ByVal sender As Object, ByVal e As System.EventArgs) Handles btnPreviousImage.Click
			_Facade.PreviousBlob()
		End Sub
	End Class
End Namespace```
The view has no longer any reference to AForge.Net. I also improved the HSBColorIndicator a bit.

```vbnet
Imports System.Drawing.Drawing2D
Imports System.Drawing
Imports TDB2009.View.Winforms.Controls.HSB

Namespace FiberColor.View.Controls.HSB
	
	Public Class HSBColorIndicator

		Private Const LEFTMARGIN As Integer = 5
		Private Const TOPMARGIN As Integer = 10
		Private Const COLORBARHEIGHT As Integer = 360
		Private Const COLORBARWIDTH As Integer = 20
		Private Const COLORBARTEXTWIDTH = 50
		Private Const ARROWWIDTH As Integer = 10
		Private Const ARROWHEIGHT As Integer = 6
		Private Const RIGHTMARGIN As Integer = 50

		Private _AverageColor As Color
		Private _Boundries As ISet(Of Boundry)

		Public Sub New()

			InitializeComponent()

			Boundries = New HashSet(Of Boundry)
			Boundries.Add(New Boundry With {.ColorName = "Red", .LowerLimit = 0, .UpperLimit = 30})
			Boundries.Add(New Boundry With {.ColorName = "Magenta", .LowerLimit = 30, .UpperLimit = 90})
			Boundries.Add(New Boundry With {.ColorName = "Blue", .LowerLimit = 90, .UpperLimit = 150})
			Boundries.Add(New Boundry With {.ColorName = "Cyan", .LowerLimit = 150, .UpperLimit = 210})
			Boundries.Add(New Boundry With {.ColorName = "Green", .LowerLimit = 210, .UpperLimit = 270})
			Boundries.Add(New Boundry With {.ColorName = "Yellow", .LowerLimit = 270, .UpperLimit = 330})
			Boundries.Add(New Boundry With {.ColorName = "Red", .LowerLimit = 330, .UpperLimit = 360})
			DrawColorImage()
		End Sub

		&lt;System.ComponentModel.Browsable(False)&gt; _
		&lt;System.ComponentModel.DesignerSerializationVisibility(System.ComponentModel.DesignerSerializationVisibility.Hidden)&gt; _
		Public Property Boundries As ISet(Of Boundry)
			Get
				Return _Boundries
			End Get
			Set(ByVal value As ISet(Of Boundry))
				_Boundries = value
				DrawColorImage()
			End Set
		End Property

		Public Property AverageColor As Color
			Get
				Return _AverageColor
			End Get
			Set(ByVal value As Color)
				_AverageColor = value
				DrawArrowAndHue()
			End Set
		End Property

		Public Overrides Property MinimumSize As System.Drawing.Size
			Get
				Return New Size(150, 400)
			End Get
			Set(ByVal value As System.Drawing.Size)
				MyBase.MinimumSize = New Size(150, 400)
			End Set
		End Property

		Private ReadOnly Property Hue As Integer
			Get
				Return Convert.ToInt32(360 - _AverageColor.GetHue)
			End Get
		End Property

		Private Sub DrawArrowAndHue()
			Me.Invalidate(New Rectangle(LEFTMARGIN + COLORBARWIDTH, TOPMARGIN, 40, COLORBARHEIGHT))
			Me.Refresh()
			If Not _AverageColor = Color.Transparent Then
				Using _Graphics = Me.CreateGraphics
					Dim points = {New Point(LEFTMARGIN + COLORBARWIDTH + COLORBARTEXTWIDTH, Hue + TOPMARGIN), New Point(LEFTMARGIN + COLORBARWIDTH + ARROWWIDTH + COLORBARTEXTWIDTH, Convert.ToInt32(Hue + TOPMARGIN - (ARROWHEIGHT / 2))), New Point(LEFTMARGIN + COLORBARWIDTH + ARROWWIDTH + COLORBARTEXTWIDTH, Convert.ToInt32(Hue + TOPMARGIN + (ARROWHEIGHT / 2)))}
					_Graphics.DrawPolygon(New Pen(Me.ForeColor), points)
					_Graphics.DrawString("Hue = " & Hue.ToString("0"), Me.Font, New SolidBrush(Me.ForeColor), LEFTMARGIN + COLORBARWIDTH + ARROWWIDTH + COLORBARTEXTWIDTH + 1, Hue + TOPMARGIN - 5)
				End Using
			End If
		End Sub

		Private Function GetColorName() As String
			For Each _Boundry In Boundries
				If Hue &gt; _Boundry.LowerLimit AndAlso Hue &lt;= _Boundry.UpperLimit Then
					Return _Boundry.ColorName
				End If
			Next
			Return "Not defined"
		End Function

		Private Sub DrawColorImage()
			Dim rect = New Rectangle(COLORBARTEXTWIDTH, 0, COLORBARWIDTH, COLORBARHEIGHT)
			Dim _ColorBarBitmap = New Bitmap(COLORBARWIDTH + COLORBARTEXTWIDTH, COLORBARHEIGHT)
			Using _Graphics = Graphics.FromImage(_ColorBarBitmap)
				Using _Brush = New LinearGradientBrush(rect, Color.Blue, Color.Red, 90.0F, False)
					Dim _Colors = {Color.Red, Color.Magenta, Color.Blue, Color.Cyan, Color.FromArgb(0, 255, 0), Color.Yellow, Color.Red}
					Dim _Positions = {0.0F, 0.1667F, 0.3372F, 0.502F, 0.6686F, 0.8313F, 1.0F}

					Dim colorBlend = New ColorBlend()
					colorBlend.Colors = _Colors
					colorBlend.Positions = _Positions
					_Brush.InterpolationColors = colorBlend

					_Graphics.FillRectangle(_Brush, rect)
					DrawBoundries(_Graphics)
				End Using
			End Using
			Me.hsbcolorbar.Top = TOPMARGIN
			Me.hsbcolorbar.Left = LEFTMARGIN
			Me.hsbcolorbar.Width = COLORBARWIDTH + COLORBARTEXTWIDTH
			Me.hsbcolorbar.Height = COLORBARHEIGHT
			Me.hsbcolorbar.Image = _ColorBarBitmap
		End Sub

		Private Sub DrawBoundries(ByRef ImageGraphics As Graphics)
			Dim penColor = Pens.White
			For Each _Boundry In Boundries
				ImageGraphics.DrawLine(penColor, COLORBARTEXTWIDTH, _Boundry.UpperLimit, COLORBARWIDTH + COLORBARTEXTWIDTH, _Boundry.UpperLimit)
				ImageGraphics.DrawString(_Boundry.ColorName, Me.Font, New SolidBrush(Me.ForeColor), 0, Convert.ToInt32(_Boundry.LowerLimit + ((_Boundry.UpperLimit - _Boundry.LowerLimit) / 2)))
			Next
		End Sub

	End Class
End Namespace```
With this as the Boundry class.

```vbnet
Namespace Controls.HSB
	&lt;Serializable()&gt;
	Public Class Boundry
		Implements IComparable

		Public Property LowerLimit As Integer
		Public Property UpperLimit As Integer
		Public Property ColorName As String

		Public Overloads Function Equals(ByVal other As Boundry) As Boolean
			If ReferenceEquals(Nothing, other) Then Return False
			If ReferenceEquals(Me, other) Then Return True
			Return other._LowerLimit = _LowerLimit AndAlso other._UpperLimit = _UpperLimit
		End Function

		Public Overrides Function GetHashCode() As Integer
			Dim hashCode As Integer = _LowerLimit
			hashCode = (hashCode * 397) Xor _UpperLimit
			Return hashCode
		End Function

		Public Overrides Function Equals(ByVal obj As Object) As Boolean
			If ReferenceEquals(Nothing, obj) Then Return False
			If ReferenceEquals(Me, obj) Then Return True
			Return Equals(TryCast(obj, Boundry))
		End Function

		Public Function CompareTo(ByVal obj As Object) As Integer Implements System.IComparable.CompareTo
			If ReferenceEquals(Nothing, obj) Then Return -1
			If ReferenceEquals(Me, obj) Then Return 0
			If Not TypeOf obj Is Boundry Then Return -1
			Return (UpperLimit + LowerLimit).CompareTo(CType(obj, Boundry).UpperLimit + CType(obj, Boundry).LowerLimit)
		End Function
	End Class
End Namespace```
Here I could have choosen to also inject the facade and let it update the usercontrol that way but for now I will just let the codebehind of the form do that.

## Conclusion

I am very happy with the current results and will move on until I have had some feedback. I don&#8217;t want to do more work for nothing if they decide it will not suit them. Like everybody else I always have more things to do than I have time so I have to balance between &#8220;good&#8221; and &#8220;good enough for now&#8221;. Perfect does not exist in the software world so I do not strive for it.