---
title: Powershell in your VB.Net application
author: Christiaan Baes (chrissie1)
type: post
date: 2011-03-20T15:45:00+00:00
ID: 1082
excerpt: |
  I had some fun this week by reading lots and lots of code. Saturday I set out to make myself a gitconsole for Visual studio 2010. I want this because powerconsole is not Async and the nugetconsole has to much nuget bits hanging on to it. 
  
  Since nuget&hellip;
url: /index.php/desktopdev/mstech/powershell-in-your-vb-net/
views:
  - 4790
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - powershell

---
I had some fun this week by reading lots and lots of code. Saturday I set out to make myself a gitconsole for Visual studio 2010. I want this because powerconsole is not Async and the nugetconsole has to much nuget bits hanging on to it. 

Since nuget is opensource I set out to get the powerconsole out of the nuget source. Alas, after spending a few hours with the code I was thinking that nuget is bit to tightly coupled with is console for me to get it out and change it the way I want it. In other words it might be easier to start from scratch. So I did some more reading and found that embedding powershell in your application is pretty easy. 

Here are two excellent articles on how to do it.

  * [How to run PowerShell scripts from C#][1]
  * [Asynchronously Execute PowerShell Scripts from C#][2]
  * [How to Embed PowerShell Within a C# Application][3]
And look what I made in a few minutes.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/gitconsole/gitconsole1.png?mtime=1300642827"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/gitconsole/gitconsole1.png?mtime=1300642827" width="398" height="300" /></a>
</div>

And here is all the code I needed to make that work. It was more work finding where they hid the System.Management.Automation.dll but nothing Google can&#8217;t handle.

```vbnet
Private Sub Button1_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button1.Click
        Dim p = InvokeCommand(Me.TextBox1.Text)
        For Each p1 In p
            Me.TextBox2.AppendText(p1)
        Next
    End Sub

    Public Function InvokeCommand(ByVal command As String) As IEnumerable(Of String)
        Try
            Return PowerShell.Create.AddScript(command).AddCommand("out-String").Invoke(Of String)()
        Catch ex As Exception
            Return New List(Of String) From {ex.Message}
        End Try
    End Function

    Private Sub Button2_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button2.Click
        Me.TextBox2.Text = ""
    End Sub```
Now I just have add this to my toolwindow in my VS extension and I&#8217;m done 😉 easy as pie.

 [1]: http://www.codeproject.com/KB/cs/HowToRunPowerShell.aspx
 [2]: http://www.codeproject.com/KB/threads/AsyncPowerShell.aspx
 [3]: http://www.dougfinke.com/blog/index.php/2010/04/08/how-to-embed-powershell-within-a-c-application/