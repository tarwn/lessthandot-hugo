---
title: 'VB.Net: Extending the image to create a thumbnail the easy way.'
author: Christiaan Baes (chrissie1)
type: post
date: 2008-07-15T08:33:10+00:00
ID: 85
url: /index.php/desktopdev/mstech/vb-net-extending-the-image-to-create-a-t/
views:
  - 2432
categories:
  - Microsoft Technologies
  - VB.NET

---
[After writing about the extensions methods for a string][1] (the basic stuff) I was having so much fun that I decided to write another one. This time for an image. I always thought the getThumbnail method of the image class was to difficult to use and to hard to remember. So I extended Image and added a ToThumbnail method to it.

First I wrote some unittests (of course)

```vbnet
Imports NUnit.Framework
Imports TDB2007.Utils.CommonFunctions.Extensions
Imports System.Drawing

Namespace Extensions
    ''' &lt;summary&gt;
    ''' A TestClass
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;TestFixture()&gt; _
Public Class TestImageExtensions

#Region " Private members "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _Image As Image
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _NewImage As Image

#End Region

#Region " Setup and TearDown "

        ''' &lt;summary&gt;
        ''' Sets up the Tests
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;SetUp()&gt; _
        Public Sub Setup()
            _Image = Nothing
            _NewImage = Nothing
        End Sub

        ''' &lt;summary&gt;
        ''' Tears down the test. Is executed after the Test is Completed
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;TearDown()&gt; _
        Public Sub TearDown()

        End Sub

#End Region

#Region " Tests "

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_To_see_if_thumbnail_is_created()
            Dim _Graphics As Graphics
            _Image = New Bitmap(100, 100)
            _Graphics = Graphics.FromImage(_Image)
            _Graphics.FillRectangle(Brushes.Blue, New Rectangle(0, 0, 100, 100))
            _Graphics.DrawLine(Pens.Black, 10, 0, 10, 100)
            Assert.IsNotNull(_Image)
            _NewImage = _Image.ToThumbnail(10)
            Assert.IsNotNull(_NewImage)
            Assert.AreEqual(10, _NewImage.Width)
            Assert.AreEqual(10, _NewImage.Height)
            Assert.AreEqual(Color.FromArgb(255, 0, 0, 255), (CType(_NewImage, Bitmap)).GetPixel(0, 0))
            Assert.AreEqual(Color.FromArgb(255, 0, 0, 229), (CType(_NewImage, Bitmap)).GetPixel(1, 1))
        End Sub

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_To_see_if_keepsaspectratio()
            Dim _Graphics As Graphics
            _Image = New Bitmap(100, 200)
            _Graphics = Graphics.FromImage(_Image)
            _Graphics.FillRectangle(Brushes.Blue, New Rectangle(0, 0, 100, 200))
            _Graphics.DrawLine(Pens.Black, 10, 0, 10, 200)
            Assert.IsNotNull(_Image)
            _NewImage = _Image.ToThumbnail(20, True)
            Assert.IsNotNull(_NewImage)
            Assert.AreEqual(10, _NewImage.Width)
            Assert.AreEqual(20, _NewImage.Height)
        End Sub

        ''' &lt;summary&gt;
        ''' A Test
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        &lt;Test()&gt; _
        Public Sub Test_To_see_if_thumbnail_doesnt_keep_aspec_ratio()
            Dim _Graphics As Graphics
            _Image = New Bitmap(100, 200)
            _Graphics = Graphics.FromImage(_Image)
            _Graphics.FillRectangle(Brushes.Blue, New Rectangle(0, 0, 100, 200))
            _Graphics.DrawLine(Pens.Black, 10, 0, 10, 200)
            Assert.IsNotNull(_Image)
            _NewImage = _Image.ToThumbnail(20, False)
            Assert.IsNotNull(_NewImage)
            Assert.AreEqual(20, _NewImage.Width)
            Assert.AreEqual(20, _NewImage.Height)
        End Sub
#End Region
    End Class
End Namespace```
I even checked to see if the thumbnail was created by checking if the color of the pixels was correct. Look at how I cheated by first running the test and then seeing which color it came up with for where the black line was suppossed to be. but since the black line was very small the pixel will be another color because of loss of data.

And now for the most important part. The Extension method

```vbnet
''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;param name="Input"&gt;&lt;/param&gt;
    ''' &lt;param name="MaximumSize"&gt;&lt;/param&gt;
    ''' &lt;param name="KeepAspectRatio"&gt;&lt;/param&gt;
    ''' &lt;returns&gt;&lt;/returns&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;Extension()&gt; _
    Public Function ToThumbnail(ByVal Input As Image, ByVal MaximumSize As Integer, Optional ByVal KeepAspectRatio As Boolean = True) As Image
        Dim ReturnImage As Image
        Dim _Callback As Image.GetThumbnailImageAbort = Nothing
        Dim _OriginalHeight As Double
        Dim _OriginalWidth As Double
        Dim _NewHeight As Double
        Dim _NewWidth As Double
        Dim _NormalImage As Image
        Dim _Graphics As Graphics

        _NormalImage = New Bitmap(Input.Width, Input.Height)
        _Graphics = Graphics.FromImage(_NormalImage)
        _Graphics.DrawImage(Input, 0, 0, Input.Width, Input.Height)
        _OriginalHeight = _NormalImage.Height
        _OriginalWidth = _NormalImage.Width
        If KeepAspectRatio = True Then
            If _OriginalHeight &gt; _OriginalWidth Then
                If _OriginalHeight &gt; MaximumSize Then
                    _NewHeight = MaximumSize
                    _NewWidth = _OriginalWidth / _OriginalHeight * MaximumSize
                Else
                    _NewHeight = _OriginalHeight
                    _NewWidth = _OriginalWidth
                End If
            Else
                If _OriginalWidth &gt; MaximumSize Then
                    _NewWidth = MaximumSize
                    _NewHeight = _OriginalHeight / _OriginalWidth * MaximumSize
                Else
                    _NewHeight = _OriginalHeight
                    _NewWidth = _OriginalWidth
                End If
            End If
        Else
            _NewHeight = MaximumSize
            _NewWidth = MaximumSize
        End If
        ReturnImage = _
            _NormalImage.GetThumbnailImage(Convert.ToInt32(_NewWidth), Convert.ToInt32(_NewHeight), _Callback, _
                                            IntPtr.Zero)
        _NormalImage.Dispose()
        _NormalImage = Nothing
        _Graphics.Dispose()
        _Graphics = Nothing
        _Callback = Nothing
        Return ReturnImage
    End Function```
And yes all the test pass. Cool huh! Perhaps not because it lacks some documentation, but then again that&#8217;s real life programming ;-).

<font color="Red">Need help with VB.Net? Come and ask a question in our <a href="http://forum.ltd.local/viewforum.php?f=39">VB.Net Forum</a></font>

 [1]: /index.php/DesktopDev/MSTech/vb-net-extending-string-with-extension-m