---
title: Printing to a zebra printer in VB.Net even when on Windows 7
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-10T07:43:00+00:00
ID: 1168
excerpt: |
  Introduction
  
  I did a post about printing to a zebra printer many moons ago (I had more hair back then). The Zebra printers I had (those are label printer if you really want to know) were connected to several windows xp machines. I use the EPL method&hellip;
url: /index.php/desktopdev/mstech/printing-to-a-zebra-printer/
views:
  - 26851
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - tlp2844
  - vb.net
  - zebra

---
## Introduction

I did a post about [printing to a zebra printer][1] many moons ago (I had more hair back then). The Zebra printers I had (those are label printer if you really want to know) were connected to several windows xp machines. I use the EPL method because it is much faster than using windows printing on theses machines. 

Yesterday I installed Windows 7 on those machines. And that caused the previous function to not work anymore. The problem is in the Createfile method and it not finding a handle anymore. You get this error when you try to print.

> Invalid parameter name&#8217; handle

As you can see in the [forum topic with the same name][2]. Some other people had the same problem. 

Many labels later I found a solution.

## The solution

After some searching and coming to my previous post to many times I found the [SharpZebra library on codeplex][3]. But for some strange reason that did not really work for my printer. It just printed nothing but did move the label one forward. So I looked at the code they used and converted it to VB.Net. After which I played with it for a while to get it to work. 

I&#8217;m not sure which part made it work for me and my [TLP2844][4] machines, since I tweaked it, but something did.

I also used [this thread on MSDN][5].

I took out the Rawprinter class sharpzebra has. Which looks like this in VB.Net.

```vbnet
Public Class RawPrinter
		Public Structure DOCINFO
			&lt;MarshalAs(UnmanagedType.LPWStr)&gt; Public DocumentName As String
			&lt;MarshalAs(UnmanagedType.LPWStr)&gt; Public OutputFile As String
			&lt;MarshalAs(UnmanagedType.LPWStr)&gt; Public DataType As String
		End Structure

		&lt;DllImport("winspool.drv", CharSet:=CharSet.Unicode, ExactSpelling:=False, CallingConvention:=CallingConvention.StdCall)&gt; _
		Private Shared Function OpenPrinter(ByVal pPrinterName As String, ByRef phPrinter As IntPtr, ByVal pDefault As Integer) As Long
		End Function

		&lt;DllImport("winspool.drv", CharSet:=CharSet.Unicode, ExactSpelling:=False, CallingConvention:=CallingConvention.StdCall)&gt; _
		Private Shared Function StartDocPrinter(ByVal hPrinter As IntPtr, ByVal Level As Integer, ByRef pDocInfo As DOCINFO) As Long
		End Function

		&lt;DllImport("winspool.drv", CharSet:=CharSet.Unicode, ExactSpelling:=True, CallingConvention:=CallingConvention.StdCall)&gt; _
		Private Shared Function StartPagePrinter(ByVal hPrinter As IntPtr) As Long
		End Function

		&lt;DllImport("winspool.drv", CharSet:=CharSet.Ansi, ExactSpelling:=True, CallingConvention:=CallingConvention.StdCall)&gt; _
		Private Shared Function WritePrinter(ByVal hPrinter As IntPtr, ByVal data As String, ByVal buf As Integer, ByRef pcWritten As Integer) As Long
		End Function

		&lt;DllImport("winspool.drv", CharSet:=CharSet.Unicode, ExactSpelling:=True, CallingConvention:=CallingConvention.StdCall)&gt; _
		Private Shared Function EndPagePrinter(ByVal hPrinter As IntPtr) As Long
		End Function

		&lt;DllImport("winspool.drv", CharSet:=CharSet.Unicode, ExactSpelling:=True, CallingConvention:=CallingConvention.StdCall)&gt; _
		Private Shared Function EndDocPrinter(ByVal hPrinter As IntPtr) As Long
		End Function

		&lt;DllImport("winspool.drv", CharSet:=CharSet.Unicode, ExactSpelling:=True, CallingConvention:=CallingConvention.StdCall)&gt; _
		Private Shared Function ClosePrinter(ByVal hPrinter As IntPtr) As Long
		End Function

		Public Shared Sub SendToPrinter(ByVal printerJobName As String, ByVal rawStringToSendToThePrinter As String, ByVal printerNameAsDescribedByPrintManager As String)
			Dim handleForTheOpenPrinter = New IntPtr()
			Dim documentInformation = New DOCINFO()
			Dim printerBytesWritten = 0
			documentInformation.DocumentName = printerJobName
			documentInformation.DataType = vbNullString
			documentInformation.OutputFile = vbNullString
			OpenPrinter(printerNameAsDescribedByPrintManager, handleForTheOpenPrinter, 0)
			StartDocPrinter(handleForTheOpenPrinter, 1, documentInformation)
			StartPagePrinter(handleForTheOpenPrinter)
			WritePrinter(handleForTheOpenPrinter, rawStringToSendToThePrinter, rawStringToSendToThePrinter.Length, printerBytesWritten)
			EndPagePrinter(handleForTheOpenPrinter)
			EndDocPrinter(handleForTheOpenPrinter)
			ClosePrinter(handleForTheOpenPrinter)
		End Sub
	End Class```
And this prints the commands I want directly to the rawprinter.

```vbnet
Dim pText As New StringBuilder
		pText.AppendLine("N")	' Start new document
		pText.AppendLine("D15")	' Set Density
		pText.AppendLine("S1")	' Set Speed
		pText.AppendLine("A30,120,0,4,2,1,N,""print text""") ' print text
		pText.AppendLine("B30,20,0,1,2,5,80,N,""print barcode""") ' print barcode
		pText.AppendLine("P1") ' number of times to print
		RawPrinter.SendToPrinter("zebra printing", pText.ToString, "\printerserverprintername") 'print the string```
For some reason I never needed to pass the N or the P in the previous version. But now I do. sharpzebra was passing a nN and perhaps that was the cause for it not working. You can find all the EPL in [the manual][6] for your printer and you can even try it out via the driversoftware.

## Conclusion

I like to thank the person(s) that made sharpzebra, it helped me a lot in solving this problem. And I hope the above will help someone else.

 [1]: /index.php/DesktopDev/MSTech/VBNET/printing-to-a-zebra-printer-from-vb-net?highlight=zebra&sentence=
 [2]: http://forum.ltd.local/viewtopic.php?f=39&t=3478
 [3]: http://sharpzebra.codeplex.com/
 [4]: http://www.zebra.com/id/zebra/na/en/index/products/printers/desktop/tlp2844.html
 [5]: http://social.msdn.microsoft.com/Forums/en-IE/vblanguage/thread/d6fbaecf-0737-49a9-979c-a9c3c0094768
 [6]: http://www.zebra.com/id/zebra/na/en/documentlibrary/manuals/en/epl2_manual__en_.File.tmp/14245L-001rA_EPL_PG.pdf?dvar1=ProductLiterature&dvar2=EPL2%20Programmers%20Manual%20(en)&dvar3=59