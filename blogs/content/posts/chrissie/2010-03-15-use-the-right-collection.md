---
title: Use the right collection
author: Christiaan Baes (chrissie1)
type: post
date: 2010-03-15T07:36:41+00:00
ID: 727
url: /index.php/desktopdev/mstech/use-the-right-collection/
views:
  - 8046
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - queue
  - vb.net

---
I was doing this in my code.

```vbnet
Private _Lines As New Collections.Specialized.StringCollection
Private Const _MaxLines As Int16 = 30
Private Sub SetMessages(ByVal message As String)
   If _Lines.Count &gt; _MaxLines Then
      _Lines.RemoveAt(_MaxLines - 1)
   End If
   _Lines.Add(DateTime.Now.ToString("HH:mm:ss") & " - " & message & Environment.NewLine)
   Dim text As New System.Text.StringBuilder
   For _temp As Integer = _Lines.Count - 1 To 0 Step -1
      text.Append(_Lines(_temp))
   Next
   Me.txtMessages.Text = text.ToString
End Sub```
Look Mama, I made a queue. 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/queue/queue.gif" alt="" title="" width="295" height="277" />
</div>

Oh wait, there is a queue object that I should have used.

```vbnet
Private _Lines As New Queue(Of String)(30)
Private Const _MaxLines As Int16 = 30 
Private Sub SetMessages(ByVal message As String)
   _Lines.Enqueue(DateTime.Now.ToString("HH:mm:ss") & " - " & message & Environment.NewLine)
   If _lines.Count &gt; _maxlines Then _lines.Dequeue()
   Me.txtMessages.Text = ""
   For Each line As String In _lines.Reverse
      Me.txtMessages.AppendText(line & Environment.NewLine)
   Next    
End Sub```
That is much shorter. Sometimes I amaze myself ;-).

Of course, it would be even shorter if I had used a ListBox.

```vbnet
Private _Lines As New Queue(Of String)(30)
Private Const _MaxLines As Int16 = 30 
Private Sub SetMessages(ByVal message As String)
   _Lines.Enqueue(DateTime.Now.ToString("HH:mm:ss") & " - " & message & Environment.NewLine)
   If _lines.Count &gt; _maxlines Then _lines.Dequeue()
   Me.ListBox1.DataSource = _lines.Reverse.ToList()    
End Sub```
So use the power of [Q][1].

Just for Denis I did a [followup on this post][2].

 [1]: http://en.wikipedia.org/wiki/File:Q_%28Star_Trek%29.jpg
 [2]: /index.php/DesktopDev/MSTech/use-the-right-collection-part-2