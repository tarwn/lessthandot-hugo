---
title: MailMessage and changing the name of an attachment
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-14T10:45:00+00:00
ID: 1043
excerpt: "Whenever my application doesn't close correctly (meaning it probably crashed) I get sent the log files at next startup via mail. In this mail I have the log files attached. Because the log files are locked at the time of sending I first take a copy of t&hellip;"
url: /index.php/desktopdev/mstech/mailmessage-and-changing-the-name/
views:
  - 5184
categories:
  - Microsoft Technologies
  - VB.NET

---
Whenever my application doesn&#8217;t close correctly (meaning it probably crashed) I get sent the log files at next startup via mail. In this mail I have the log files attached. Because the log files are locked at the time of sending I first take a copy of them like this, you can take a copy of a file while it is locked.

```vbnet
For Each _Filename As String In _attachements
  _Filename = Environment.CurrentDirectory & "" & _Filename
  If System.IO.File.Exists(_Filename) Then
    If System.IO.File.Exists(_Filename & ".copy") Then
      System.IO.File.Delete(_Filename & ".copy")
    End If
    System.IO.File.Copy(_Filename, _Filename & ".copy")
    Dim _attachment As New System.Net.Mail.Attachment(_Filename & ".copy")
    _message.Attachments.Add(_attachment)
    _message.Body &= "&lt;p&gt;Attached file: " & _Filename & "&lt;/p&gt;"
  Else
    _message.Body &= "&lt;p&gt;File not attached: " & _Filename & " because file does not exists.&lt;/p&gt;"
  End If
Next```
Of course this means that the filename in the mail will end in copy (which means it will not open in your favorite program when you double click it) or not be the same as the original or some other reason. This is not really a problem if you know how to change the name back to the original name which you can see in the following piece of code.

```vbnet
For Each _Filename As String In _attachements
  _Filename = Environment.CurrentDirectory & "" & _Filename
  If System.IO.File.Exists(_Filename) Then
    If System.IO.File.Exists(_Filename & ".copy") Then
      System.IO.File.Delete(_Filename & ".copy")
    End If
    System.IO.File.Copy(_Filename, _Filename & ".copy")
    Dim _attachment As New System.Net.Mail.Attachment(_Filename & ".copy")
    _attachment.Name = _Filename
    _message.Attachments.Add(_attachment)
    _message.Body &= "&lt;p&gt;Attached file: " & _Filename & "&lt;/p&gt;"
  Else
    _message.Body &= "&lt;p&gt;File not attached: " & _Filename & " because file does not exists.&lt;/p&gt;"
  End If
Next```
Did you see what changed?

<code class="codespan">_attachment.Name = _Filename</code> 

So you can change the filename of the attachment that is set automatically to the name of the file you use as the attachment to something different by changing the Name property of the attachment.

Great, now on to bigger and better things.

Why is this important? I have no idea, I just wanted to share this with you guys. I guess it&#8217;s one of those things you are unaware of until you think you are going to need it and then have to look it up and then realize while writing a blogpost it is just stupid. That&#8217;s why writing technical blogs is so much fun and it makes you look stupid.