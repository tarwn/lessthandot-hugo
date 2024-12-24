---
title: 'Another hidden gem:  CopyFromScreen'
author: Christiaan Baes (chrissie1)
type: post
date: 2011-09-19T12:20:00+00:00
ID: 1323
excerpt: |
  And I found something else on StackOverflow that I didn't know existed.
  For this Stackoverflow user there seemed to be a problem when using CopyFromScreen.
url: /index.php/desktopdev/mstech/another-hidden-gem-copyfromscreen/
views:
  - 4400
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - copyfromscreen
  - vb.net

---
And I found something else on StackOverflow that I didn&#8217;t know existed.

For [this Stackoverflow user][1] there seemed to be a problem when using CopyFromScreen. 

I used to use the GDI+ version for this. Which this might well just be a wrapper for.

But for me this code works just fine.

```vbnet
Public Class Form1

	Private Sub Form1_Load(sender As System.Object, e As EventArgs) Handles MyBase.Load
		Dim screenSize = SystemInformation.PrimaryMonitorSize
		Dim bitmap = New Bitmap(screenSize.Width, screenSize.Height)
		Using g As Graphics = Graphics.FromImage(bitmap)
			g.CopyFromScreen(New Point(0, 0), New Point(0, 0), screenSize)
		End Using
		PictureBox1.Image = bitmap
		bitmap.Save("c:tempscreenshot.png", Imaging.ImageFormat.Png)
	End Sub

End Class

```
As you can see in this screenshot.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/CopyFromScreen/copyfromscreen.png?mtime=1316441848"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/CopyFromScreen/copyfromscreen.png?mtime=1316441848" width="547" height="437" /></a>
</div>

And the file is saved too.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/CopyFromScreen/copyfromscreen1.png?mtime=1316441927"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/CopyFromScreen/copyfromscreen1.png?mtime=1316441927" width="848" height="734" /></a>
</div>

So I don&#8217;t know what the problem is the user is having but I know it works ;-).

 [1]: http://stackoverflow.com/questions/7472192/graphics-copyfromscreen-creates-a-blank-image