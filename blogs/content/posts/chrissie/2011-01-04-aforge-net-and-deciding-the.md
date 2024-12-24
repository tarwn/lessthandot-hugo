---
title: Aforge.Net and deciding the average color of a fiber
author: Christiaan Baes (chrissie1)
type: post
date: 2011-01-04T09:10:00+00:00
ID: 994
excerpt: |
  Introduction
  
  Yesterday Mladen Prajdic (twitter|blog) told me about AForge.Net for image manipulation. So I immediatly downloaded it and tried it out on one of my pet projects. Finding the fiber and determining the average color based on the image.&hellip;
url: /index.php/desktopdev/mstech/aforge-net-and-deciding-the/
views:
  - 12245
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - aforge.net
  - vb.net

---
## Introduction

Yesterday Mladen Prajdic ([twitter][1]|[blog][2]) told me about [AForge.Net][3] for image manipulation. So I immediately downloaded it and tried it out on one of my pet projects. Finding the fiber and determining the average color based on the image.

## The mission

The mission , if you wish to accept it, is to calculate the average color of a fiber as to eliminate the need for the analyst to decide the color. The problem with letting the analyst decide the color is that it changes from time to time and from person to person. Orange might be red in the beginning of the day it might be yellow by the end of the day. Turquoise might be blue one day or green the other. We have already limited the number of colors an analyst can choose from but still it is not good enough. In the end of the day we want to compare our thousands of fibers in an easy, fast and reliable way. Make groupings based on those characteristics.

## Solving it with AForge.Net

[AForge.Net][3] is a .Net library for Image manipulation and so much more. It has good documentation which helped me a lot to get this up and running in a couple of hours. It has however a huge amount of methods to wade through. But I got there in the end. I don&#8217;t know why I did not know about this earlier.

## A first solution

I will show you what I did to solve my little problem very quickly. Pretty sure this will need a lot more work before it even goes in production.

### The victim

Lets start with an easy one.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber1.png?mtime=1294137908"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber1.png?mtime=1294137908" width="371" height="403" /></a>
</div>

This is a synthetic fiber that has a nice blue color. Should be easy enough. As you can see the background is far from ideal but the contrast is big enough. Since I only want to measure the color I will not try to find colorless fibers which makes this a lot easier to begin with.

### The background

So first thing to do was to eliminate the background. Or just making it a uniform color would help a lot too.

I used a color filter to do this for me.

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
End Try```
The GetBitmap method looks like this.

```vbnet
Private Function GetBitmap() As Bitmap
		Dim b = New Bitmap(PictureBox2.Image.Width, PictureBox2.Image.Height, System.Drawing.Imaging.PixelFormat.Format24bppRgb)
		Dim g = Graphics.FromImage(b)
		g.DrawImage(PictureBox2.Image, 0, 0, New Rectangle(0, 0, PictureBox2.Image.Width, PictureBox2.Image.Height), GraphicsUnit.Pixel)
		Return b
	End Function```
It also makes the image 24bppRgb because that&#8217;s what the documentation says it needs. And you should read the documentation before you begin, like me ;-).

This is the result of the color filter.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber2.png?mtime=1294138665"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber2.png?mtime=1294138665" width="753" height="544" /></a>
</div>

As you can see there are still a few artifacts that I need to get rid of.

I got rid of it by doing a ExtractBiggestBlob.

```vbnet
Dim i = New AForge.Imaging.Filters.ExtractBiggestBlob
		Dim b = GetBitmap()
		Try
			PictureBox2.Image = i.Apply(b)
			Me.lblStatus.Text = i.BlobPosition.ToString
		Catch ex As Exception
			MessageBox.Show(ex.Message)
		End Try```
Which gave me this result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber3.png?mtime=1294138885"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber3.png?mtime=1294138885" width="753" height="544" /></a>
</div>

### Average color

Now I just have to total up the RGB values and make an average of them not counting the black pixels.

```vbnet
Dim b = GetBitmap()
		Dim count = 0
		Dim totalr As Double = 0
		Dim totalg As Double = 0
		Dim totalb As Double = 0
		Dim tempcolor As color
		For i as integer = 0 to b.height-1
			For j As Integer = 0 to b.Width-1
				tempcolor = b.GetPixel(j,i)
				Dim color As Integer = Convert.ToInt32(tempcolor.r) + tempcolor.G + tempcolor.B
				If  color &gt; 0 then
					totalr += tempcolor.R
					totalg += tempcolor.G
					totalb += tempcolor.B
					count+=1
				End If
			Next
		Next
		Me.lblaveragecolor.BackColor = color.FromArgb(Convert.ToInt32(totalr/count),convert.ToInt32(totalg/count),Convert.ToInt32(totalr/count))```
Something like this will do.

The color is shown in the statusbar.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber3.png?mtime=1294138885"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/Findfiber/FindFiber3.png?mtime=1294138885" width="753" height="544" /></a>
</div>

## Conclusion

The AForge.Net library is well documented and easy to use. In some cases it was oversimplified but I got a result fairly quickly. A special thanks to Andrew Kirillov who I believe to be the author of AForge.Net. I could not find a blog in his name.

 [1]: http://twitter.com/#!/MladenPrajdic
 [2]: http://weblogs.sqlteam.com/mladenp/
 [3]: http://www.aforgenet.com/