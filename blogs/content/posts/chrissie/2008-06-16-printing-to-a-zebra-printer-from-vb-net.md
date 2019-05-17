---
title: Printing to a zebra printer from VB.Net
author: Christiaan Baes (chrissie1)
type: post
date: 2008-06-16T08:25:00+00:00
ID: 63
excerpt: |
  This code is based on code I found from Rick Chronister, but I can't find the article anymore. It was also using a deprecated method. I adapted to look like this.
  
  Imports System.IO
  Imports System.Runtime.InteropServices
  
  Namespace ZebraLabels&hellip;
url: /index.php/desktopdev/mstech/printing-to-a-zebra-printer-from-vb-net/
views:
  - 32961
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - tlp2844
  - vb.net
  - zebra

---
This code is based on code I found from Rick Chronister, but I can&#8217;t find the article anymore. It was also using a deprecated method. I adapted to look like this.

```vbnet
Imports System.IO
Imports System.Runtime.InteropServices

Namespace ZebraLabels
    ''' &lt;summary&gt;
    ''' This class can print zebra labels to either a network share, LPT, or COM port.
    ''' 
    ''' Programmer: Rick Chronister
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;Only tested for network share, but in theory works for LPT and COM.&lt;/remarks&gt;
    Public Class ZebraPrint

#Region " Private constants "
        Private Const GENERIC_WRITE As Integer = &H40000000
        Private Const OPEN_EXISTING As Integer = 3
#End Region

#Region " Private members "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _SafeFileHandle As Microsoft.Win32.SafeHandles.SafeFileHandle
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _fileWriter As StreamWriter
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _outFile As FileStream
#End Region

#Region " private structures "
        ''' &lt;summary&gt;
        ''' Structure for CreateFile.  Used only to fill requirement
        ''' &lt;/summary&gt;
        &lt;StructLayout(LayoutKind.Sequential)&gt; _
        Public Structure SECURITY_ATTRIBUTES
            Private nLength As Integer
            Private lpSecurityDescriptor As Integer
            Private bInheritHandle As Integer
        End Structure
#End Region

#Region " com calls "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;param name="lpFileName"&gt;&lt;/param&gt;
        ''' &lt;param name="dwDesiredAccess"&gt;&lt;/param&gt;
        ''' &lt;param name="dwShareMode"&gt;&lt;/param&gt;
        ''' &lt;param name="lpSecurityAttributes"&gt;&lt;/param&gt;
        ''' &lt;param name="dwCreationDisposition"&gt;&lt;/param&gt;
        ''' &lt;param name="dwFlagsAndAttributes"&gt;&lt;/param&gt;
        ''' &lt;param name="hTemplateFile"&gt;&lt;/param&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private Declare Function CreateFile Lib "kernel32" Alias "CreateFileA" (ByVal lpFileName As String, ByVal dwDesiredAccess As Integer, ByVal dwShareMode As Integer, &lt;MarshalAs(UnmanagedType.Struct)&gt; ByRef lpSecurityAttributes As SECURITY_ATTRIBUTES, ByVal dwCreationDisposition As Integer, ByVal dwFlagsAndAttributes As Integer, ByVal hTemplateFile As Integer) As Microsoft.Win32.SafeHandles.SafeFileHandle
#End Region

#Region " Public methods "
        ''' &lt;summary&gt;
        ''' This function must be called first.  Printer path must be a COM Port or a UNC path.
        ''' &lt;/summary&gt;
        Public Sub StartWrite(ByVal printerPath As String)
            Dim SA As SECURITY_ATTRIBUTES

            'Create connection
            _SafeFileHandle = CreateFile(printerPath, GENERIC_WRITE, 0, SA, OPEN_EXISTING, 0, 0)

            'Create file stream
            Try
                _outFile = New FileStream(_SafeFileHandle, FileAccess.Write)
                _fileWriter = New StreamWriter(_outFile)
            Catch ex As Exception
                System.Windows.Forms.MessageBox.Show("Can not find printer.", "Warning", Windows.Forms.MessageBoxButtons.OK, Windows.Forms.MessageBoxIcon.Error, Windows.Forms.MessageBoxDefaultButton.Button1)
            End Try

        End Sub

        ''' &lt;summary&gt;
        ''' This will write a command to the printer.
        ''' &lt;/summary&gt;
        Public Sub Write(ByVal rawLine As String)
            If _fileWriter IsNot Nothing Then
                _fileWriter.WriteLine(rawLine)
            End If
        End Sub

        ''' &lt;summary&gt;
        ''' This function must be called after writing to the zebra printer.
        ''' &lt;/summary&gt;
        Public Sub EndWrite()
            'Clean up
            If _fileWriter IsNot Nothing Then
                _fileWriter.Flush()
                _fileWriter.Close()
                _outFile.Close()
            End If
            _SafeFileHandle.Close()
            _SafeFileHandle.Dispose()

        End Sub
#End Region

    End Class
End Namespace
```
You use it like so.

```vbnet
Dim _print as new ZebraPrint
_print.StartWrite("//printserver//zebra001")
_print.Write("Print test.")
_print.EndWrite()```
You can also write barcodes with it.

like this

```vbnet
Dim _print as new ZebraPrint
_print.StartWrite("//printserver//zebra001")
dim _Text as String = "Print test"
_print.Write("A30,120,0,4,2,1,N,""" & _Text & """")
_print.EndWrite()```
You can find all the code characters in your zebra manual.

Since you are using the zebra printer directly it prints very fast. A lot faster then when you are using the native windows driver.

Edit: I will have to close the comments on this post but I will open [a topic in the forum][1] for this.

Edit2: I have a new blogpost to describe how to print your EPL commands when the printer is [connected to a windows 7 machine][2].

 [1]: http://forum.lessthandot.com/viewtopic.php?f=39&t=3478
 [2]: /index.php/DesktopDev/MSTech/printing-to-a-zebra-printer