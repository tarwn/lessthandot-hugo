---
title: pdfviewernet, where have you been all my life?
author: Christiaan Baes (chrissie1)
type: post
date: 2011-11-10T06:39:00+00:00
ID: 1379
excerpt: |
  I use the acrobat reader activex control to show pdf's in my winforms application. And yesterday I was bitching about it on twitter because it for some reason was blocking a folder on my buildserver every time I ran a build. At that point in time I could either choose to avoid the tests that made this happen or just use a hammer and get rid of the adobe acrobat reader at the end of my build. So I bitched about this on twitter, as one always does, and I got some very useful replies. 
  And someone suggested pdfviewernet and that's what I'm using now.
url: /index.php/desktopdev/mstech/pdfviewernet-where-have-you-been/
views:
  - 11969
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - acrobat reader activex
  - pdfvieweret
  - twitter
  - vb.net

---
## Introduction

I use the acrobat reader activex control to show pdf&#8217;s in my winforms application. And yesterday I was bitching about it on twitter because it for some reason was blocking a folder on my buildserver every time I ran a build. At that point in time I could either choose to avoid the tests that made this happen or just use a hammer and get rid of the adobe acrobat reader at the end of my build. So I bitched about this on twitter, as one always does, and I got some very useful replies.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet.png?mtime=1320912937"><img alt="" src="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet.png?mtime=1320912937" width="508" height="727" /></a>
</div>

And did you see the suggestion by the ever brilliant [Jeremiah Peschka][1]. So I did what he said and checked that project out.

## Pdfviewernet

You can find [pdfviewernet on google code][2]. First thing you&#8217;ll note is that it is written in VB.Net which is brilliant. Second thing to note is that the downloads have a very old date on them. So I decided to install tortoisesvn again and compile against the trunk.

Sadly it isn&#8217;t on nuget (yet). 

This is very easy and if you then open and run it you even get a little demo app to play with.

I opened a pdf with it.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet2.png?mtime=1320913623"><img alt="" src="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet2.png?mtime=1320913623" width="790" height="640" /></a>
</div>

And I did the OCR thing on that pdf file.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet3.png?mtime=1320913634"><img alt="" src="/wp-content/uploads/users/chrissie1/pdfviewernet/pdfviewernet3.png?mtime=1320913634" width="496" height="964" /></a>
</div>

I must say that the sample app didn&#8217;t do the OCR. I had to change the click event for that button to this. But that was a minor thing.

```
Private Sub btOCR_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btOCR.Click
        MsgBox(PdfViewer1.OCRCurrentPage)
 End Sub```
Over all it works great and fast and easy.

Just place the viewer control on your form. Add a browsebutton and do this in click event of that button.

```vbnet
PdfViewer1.SelectFile()```
And that&#8217;s it. Just works.

## Conclusion

When I was looking for a solution for showing my pdf files in an easy and fast way I never came across pdfviewernet, but now I&#8217;m happy I bitched about acrobat reader activex on twitter.

I love it already. I just wish it was on nuget. Many thanks to [Jeremiah Peschka][1], [Mladen Prajdic][3], [Rob Sullivan][4] and [Mark S. Rasmussen][5]. You guys and twitter in general are awesome.

 [1]: https://twitter.com/#!/peschkaj
 [2]: http://code.google.com/p/pdfviewernet/
 [3]: https://twitter.com/#!/MladenPrajdic
 [4]: https://twitter.com/#!/DataChomp
 [5]: https://twitter.com/#!/improvedk