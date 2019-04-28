---
title: Mailmessage and the Newline that was being ignored
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-01T09:02:00+00:00
ID: 1025
excerpt: |
  In my app I am sending some mails to myself just so I would feel more important. And I was having a weird problem. This is the code I was using.
  
  
  Dim _message As MailMessage = New MailMessage(_sender, _recipient, _subject, "")
  _message.IsBodyHtml =&hellip;
url: /index.php/desktopdev/mstech/mailmessage-and-the-newline-that/
views:
  - 2251
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - isbodyhtml
  - mailmessage
  - vb.net

---
In my app I am sending some mails to myself just so I would feel more important. And I was having a weird problem. This is the code I was using.

```vbnet
Dim _message As MailMessage = New MailMessage(_sender, _recipient, _subject, "")
_message.IsBodyHtml = True
_message.Body &= "Attached file: " & _Filename & ".txt" & Environment.NewLine
```
To my surprise this this was the result.

```
Attached file: filename.txtAttached file: filename2.txt```
My emailreader was not really spotting the newlines. 

Of course the fault was very stupid and it was easy to fix. 

The reason the newline is ignored is because of the <code class="codespan">IsBodyHtml = True</code> That one says my body will be sent as HTML and the HTML is ignoring the newline. To make it valid HTML and more readable I can add a br tag to the end.

```vbnet
Dim _message As MailMessage = New MailMessage(_sender, _recipient, _subject, "")
_message.IsBodyHtml = True
_message.Body &= "Attached file: " & _Filename & ".txt" & "&lt;br /&gt;"
```
Or better still wrap it in p tags.

```vbnet
Dim _message As MailMessage = New MailMessage(_sender, _recipient, _subject, "")
_message.IsBodyHtml = True
_message.Body &= "&lt;p&gt;Attached file: " & _Filename & ".txt&lt;/p&gt;"
```
Or I could just say <code class="codespan">IsBodyHtml = False</code> and leave the newlines in there all together.