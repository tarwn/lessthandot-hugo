---
title: ITextSharp and the HeaderFooter on the first page
author: Christiaan Baes (chrissie1)
type: post
date: 2009-08-19T04:42:30+00:00
ID: 541
excerpt: |
  Yesterday I had some fun with iTextSharp. I have to confess that it does a good job for what I want it to do. But I got a bit confused with the Header and Footer thing. I use version 4.1.2.
  
  Private Sub Button1_Click(ByVal sender As System.Object, ByV&hellip;
url: /index.php/desktopdev/mstech/itextsharp-and-the-headrefooter-on-the-f/
views:
  - 33723
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - itextsharp
  - pdf
  - vb.net

---
Yesterday I had some fun with [iTextSharp][1]. I have to confess that it does a good job for what I want it to do. But I got a bit confused with the Header and Footer thing. I use version 4.1.2.

```vbnet
Private Sub Button1_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button1.Click
        Dim oDoc As New iTextSharp.text.Document(iTextSharp.text.PageSize.A4)
        PdfWriter.GetInstance(oDoc, New FileStream("Nofooteronfirstpage.pdf", FileMode.Create))
        oDoc.Open()
        Dim footer As New HeaderFooter(New Phrase("This is page: "), True)
        oDoc.Footer = footer
        Dim header As New HeaderFooter(New Phrase("Image scanned on " & Now.ToString("dd/MM/yyyy") & " by " & Environment.UserName), False)
        oDoc.Header = header
        For i As Integer = 1 To 2
            oDoc.Add(New Phrase("some random text" & i))
            oDoc.NewPage()
        Next
        oDoc.Close()
    End Sub```
The above code does not print a header or footer on the first page and it seemed to me that it should.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/itextsharp/pdfnoimageonfirstpage.png" alt="" title="" width="549" height="928" />
</div>

But I found the solution in the end.

```vbnet
Private Sub Button2_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button2.Click
        Dim oDoc As New iTextSharp.text.Document(iTextSharp.text.PageSize.A4)
        PdfWriter.GetInstance(oDoc, New FileStream("footeronfirstpage.pdf", FileMode.Create))
        Dim footer As New HeaderFooter(New Phrase("This is page: "), True)
        oDoc.Footer = footer
        Dim header As New HeaderFooter(New Phrase("Image scanned on " & Now.ToString("dd/MM/yyyy") & " by " & Environment.UserName), False)
        oDoc.Header = header
        oDoc.Open()
        For i As Integer = 1 To 2
            oDoc.Add(New Phrase("some random text" & i))
            oDoc.NewPage()
        Next
        oDoc.Close()
    End Sub```
Can you see the difference? Ok, I&#8217;ll tell you. The Document is no longer opened before setting the header and footer but after setting the header and footer.

Which then gave this result.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/itextsharp/pdffooteronfirstpage.png" alt="" title="" width="549" height="928" />
</div>

 [1]: http://itextsharp.sourceforge.net/