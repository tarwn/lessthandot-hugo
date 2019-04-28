---
title: Printing to a zebra printer that uses ZPL on windows using VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2012-04-11T08:37:00+00:00
ID: 1594
excerpt: Printing to zebra printers is much easier if you use the built in commands. It prints much faster than using the normal windows drivers and sometimes that is very important when you have to print tons of these little labels a day.
url: /index.php/desktopdev/mstech/printing-to-a-zebra-printer-1/
views:
  - 16250
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - gx430t
  - zebra
  - zpl

---
This post is for the people that have this type of printer, others can read it to see the nice things that are out there and drool over all this awesomeness you will never have.

Today I got a new toy to play with <happypanda>, since the old toy broke down <sadpanda>. And I got a new [Zebra GX430t labelprinter][1]. The problem is that the new printer does not support EPL2 like the older TLP 2844. It supports the newer (I guess) ZPL.

That&#8217;s nice but that means I have to check which printer I&#8217;m printing to and send it the right commands. Anyway.

First thing to do is to see if it still works.

And guess what? The documentation sucks, nothing new there.

But I got the codes to work and I didn&#8217;t have to change anything in my printraw class

And here it is again.

```vbnet
Imports System.Runtime.InteropServices
Imports System.Text

Public Class PrintRaw

    Public Sub Print(ByVal codes As StringBuilder, ByVal printer As String)
        SendToPrinter("Printing zebras", codes.ToString, printer)
    End Sub

    Private Structure Docinfo
        &lt;MarshalAs(UnmanagedType.LPWStr)&gt; Public DocumentName As String
        &lt;MarshalAs(UnmanagedType.LPWStr)&gt; Public OutputFile As String
        &lt;MarshalAs(UnmanagedType.LPWStr)&gt; Public DataType As String
    End Structure

    &lt;DllImport("winspool.drv", CharSet:=CharSet.Unicode, ExactSpelling:=False, CallingConvention:=CallingConvention.StdCall)&gt; _
    Private Shared Function OpenPrinter(ByVal pPrinterName As String, ByRef phPrinter As IntPtr, ByVal pDefault As Integer) As Long
    End Function

    &lt;DllImport("winspool.drv", CharSet:=CharSet.Unicode, ExactSpelling:=False, CallingConvention:=CallingConvention.StdCall)&gt; _
    Private Shared Function StartDocPrinter(ByVal hPrinter As IntPtr, ByVal level As Integer, ByRef pDocInfo As Docinfo) As Long
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

    Private Sub SendToPrinter(ByVal printerJobName As String, ByVal rawStringToSendToThePrinter As String, ByVal printerNameAsDescribedByPrintManager As String)
        Dim handleForTheOpenPrinter = New IntPtr()
        Dim documentInformation = New Docinfo()
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

End Class

```
And this is how you print a code128 barcode with a text.

```vbnet
Imports System.Text

Module Module1

    Sub Main()
        Dim p As New PrintRaw
        Dim s As New StringBuilder
        s.AppendLine("^XA")
        s.AppendLine("^PR30")
        s.AppendLine("^FO0,50")
        s.AppendLine("^BCN,60,N,N,N,N")
        s.AppendLine("^FDtext")
        s.AppendLine("^FS")
        s.AppendLine("^FO0,150")
        s.AppendLine("^ADN,36,20")
        s.AppendLine("^FDtext")
        s.AppendLine("^FS")
        s.AppendLine("^XZ")
        p.Print(s, "\tex11Zebra003")
    End Sub

End Module
```
The code ^XA has to be there to start the session, and code ^XZ closes it.

What&#8217;s important is that you need an ^FS between the barcode and the text to print. Else it just prints the barcode and not the text.

^FO stands for the position. And anything with ^B is a barcode. Anything with Â is text. 

It&#8217;s quit amazing how something like that can become readable after a while. I guess you just adapt, even to the weirdest things. And it is very fast and cool. 

Here are 2 previous posts about the Zebra topic.

[Printing to a zebra printer in VB.Net even when on Windows 7][2]
  
[Printing to a zebra printer from VB.Net][3]</sadpanda></happypanda>

 [1]: http://www.zebra.com/id/zebra/na/en/index/products/printers/desktop/gx430t.html
 [2]: /index.php/DesktopDev/MSTech/printing-to-a-zebra-printer
 [3]: /index.php/DesktopDev/MSTech/VBNET/printing-to-a-zebra-printer-from-vb-net