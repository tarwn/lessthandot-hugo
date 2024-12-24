---
title: Use the right collection part 2
author: Christiaan Baes (chrissie1)
type: post
date: 2010-03-16T07:25:14+00:00
ID: 728
url: /index.php/desktopdev/mstech/use-the-right-collection-part-2/
views:
  - 1572
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - performance
  - queue
  - vb.net

---
Yesterday [I made a post][1] about using a queue instead of a list because the code was so much cleaner. I think it is very important that code is clean and easier to read. 

But then Denis asked about the performance differences between the 2 methods. I hadn&#8217;t worried about the performance, because in my case the performance was good in both cases.

But I aim to please my fellow LTDers, so I did a little performance test. And let me remind you that like most performance tests, this one is flawed as well.

I made a form with 2 listboxes and 2 textboxes, to show the results. And 1 listbox to show the timings. And a button to start the process.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/queue/queue1.png" alt="" title="" width="1079" height="374" />
</div>

And here is the code to go with it.

```vbnet
Public Class Form1

    Dim _lines1 As New Queue(Of String)
    Dim _lines2 As New Queue(Of String)
    Dim _lines3 As New List(Of String)
    Dim _lines4 As New List(Of String)

    Private Const _maxlines As Integer = 10
    Dim _total As Integer = 1000

    Private Sub Form1_Load(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles MyBase.Load
        
    End Sub

    Private Sub Button1_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button1.Click
        Dim _StopWatch As New Stopwatch
        _StopWatch.Start()
        For _Counter = 0 To _total
            _lines1.Enqueue(_Counter.ToString)
            If _lines1.Count &gt; _maxlines Then _lines1.Dequeue()
            Me.ListBox1.DataSource = _lines1.Reverse.ToList()
        Next
        _StopWatch.Stop()
        Me.TextBox3.Text = "queue with listbox: " & _StopWatch.Elapsed.ToString & Environment.NewLine
        _StopWatch.Reset()
        _StopWatch.Start()
        Dim text1 As System.Text.StringBuilder
        For _Counter = 0 To _total
            _lines2.Enqueue(_Counter.ToString)
            If _lines2.Count &gt; _maxlines Then _lines2.Dequeue()
            Me.TextBox1.Text = ""
            text1 = New System.Text.StringBuilder
            For _temp As Integer = _lines2.Count - 1 To 0 Step -1
                text1.Append(_lines2(_temp) & Environment.NewLine)
            Next
            Me.TextBox1.Text = text1.ToString
        Next
        _StopWatch.Stop()
        Me.TextBox3.AppendText("queue with textbox: " & _StopWatch.Elapsed.ToString & Environment.NewLine)
        _StopWatch.Reset()
        _StopWatch.Start()
        For _Counter = 0 To _total
            If _lines4.Count &gt;= _maxlines Then
                _lines4.RemoveAt(0)
            End If
            _lines4.Add(_Counter.ToString)
            Me.ListBox2.DataSource = (From en In _lines4.ToList Order By Convert.ToInt32(en) Descending Select en).ToList
        Next
        _StopWatch.Stop()
        Me.TextBox3.AppendText("list with listbox: " & _StopWatch.Elapsed.ToString & Environment.NewLine)
        _StopWatch.Reset()
        _StopWatch.Start()
        Dim text2 As System.Text.StringBuilder
        For _Counter = 0 To _total
            If _lines3.Count &gt;= _maxlines Then
                _lines3.RemoveAt(0)
            End If
            _lines3.Add(_Counter.ToString)
            Me.TextBox2.Text = ""
            text2 = New System.Text.StringBuilder
            For _temp As Integer = _lines3.Count - 1 To 0 Step -1
                text2.Append(_lines3(_temp) & Environment.NewLine)
            Next
            Me.TextBox2.Text = text2.ToString
        Next
        _StopWatch.Stop()
        Me.TextBox3.AppendText("list with textbox: " & _StopWatch.Elapsed.ToString & Environment.NewLine)
    End Sub
End Class
```
Clicking on &#8220;run&#8221; gives me the following result.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/queue/queue2.png" alt="" title="" width="1079" height="374" />
</div>

And here are some more numbers.

Test 2

> queue with listbox: 00:00:00.3433968
  
> queue with textbox: 00:00:00.1383573
  
> list with listbox: 00:00:00.3251502
  
> list with textbox: 00:00:00.1366604

Test 3

> queue with listbox: 00:00:00.4358506
  
> queue with textbox: 00:00:00.1386994
  
> list with listbox: 00:00:00.3229863
  
> list with textbox: 00:00:00.1359696

What does this prove? Well not much. The Queue method has pretty much the same performance than the List method. But the binding to the listbox is much slower.

Is this something to worry about? <span class="MT_red">Nope.</span>

Conclusion.

Do we use a List or a Queue? In this case we use a Queue because the code is much cleaner.

 [1]: /index.php/DesktopDev/MSTech/use-the-right-collection