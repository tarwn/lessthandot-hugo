---
title: Why one DefaultPageSettings is not the same as the next.
author: Christiaan Baes (chrissie1)
type: post
date: 2012-03-12T07:29:00+00:00
ID: 1554
excerpt: "Where one DefaultPageSettings works and the other doesn't."
url: /index.php/desktopdev/mstech/why-one-defaultpagesettings-is-not/
views:
  - 4787
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - printdocument
  - printpreviewdialog
  - vb.net
  - winforms

---
From time to time on still has to use the built in Print abilities of winforms. But you should be using microsoft reporting a lot more. 

And one of the things you micht want to do is to print something in lanscape mode.

So you do this.

```vbnet
Dim _printDocument = New Drawing.Printing.PrintDocument
Dim _printPreviewDialog = New PrintPreviewDialog
_printPreviewDialog.Document = _printDocument
_printDocument.PrinterSettings.DefaultPageSettings.Landscape = True
_printPreviewDialog.ShowDialog(Me)```
But alas that doesn&#8217;t actually turn the page. And God only knows what it really does because I have no idea.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/landscape/landscape1.png?mtime=1331544512"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/landscape/landscape1.png?mtime=1331544512" width="390" height="378" /></a>
</div>

But luckily we can do this instead.

```vbnet
Dim _printDocument = New Drawing.Printing.PrintDocument
Dim _printPreviewDialog = New PrintPreviewDialog
_printPreviewDialog.Document = _printDocument
_printDocument.DefaultPageSettings.Landscape = True
_printPreviewDialog.ShowDialog(Me)```
And yes that gives the desired result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/landscape/landscape2.png?mtime=1331544522"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/landscape/landscape2.png?mtime=1331544522" width="416" height="338" /></a>
</div>

Did you spot the difference?

Well, you don&#8217;t use the PrinterSettings.DefaultPageSettings on PrintDocument but you use the DefaultPageSettings on PrintDocument to get it to work. Someone might need to rethink the ApI there, but I guess that has no chance what so ever of happening since winforms is supposedly dead.