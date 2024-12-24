---
title: Powershell in your VB.Net application the better way
author: Christiaan Baes (chrissie1)
type: post
date: 2011-03-21T12:10:00+00:00
ID: 1083
excerpt: |
  In yesterdays blogpost I showed you how to add powershell to your VB.Net application the esay way.
  
  Today I will show you a better way to do this. 
  
  You might have noticed in the previous version that the system was waiting for your statement to fin&hellip;
url: /index.php/desktopdev/mstech/powershell-in-your-vb-net-1/
views:
  - 7058
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - embed
  - powershell
  - vb.net

---
In [yesterdays blogpost][1] I showed you how to add powershell to your VB.Net application the esay way.

Today I will show you a better way to do this. 

You might have noticed in the previous version that the system was waiting for your statement to finish before it showed anything on the screen, in other words it was doing this in an synchronized way, this is how the powerconsole extension for Visual studio currently works. It would be better to do this in an asyncronized manner, like the nuget console. 

It isn&#8217;t all that hard to accomplish this but it need some extra code an a diffent way of working. First of all you will need to use the Runspace and pipelines powershell makes available for you. 

I refined my code a little So I can easily choose which one to run.

Here is how the app looks.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/gitconsole/gitconsole2.png?mtime=1300715749"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/gitconsole/gitconsole2.png?mtime=1300715749" width="684" height="420" /></a>
</div>

I added a button to run sync and async . And a button with some sample code you can run. In this version BTW write-host will not really work. But write will so use that.

To start with I made an interface so I could easily run one or the other.

```vbnet
Namespace PowerShell
	Public Interface IRun
		Sub Run(ByVal Command As String)
		Event OutputChanged(ByVal Result As String)
		Event RunFinished()
	End Interface
End Namespace```
Then I implemented then sync way.

```vbnet
Imports System.Text

Namespace PowerShell
	Public Class RunSync
		Implements IRun
		
		Public Sub Run(ByVal Command As String) Implements IRun.Run
			Dim _Result = New StringBuilder
			Dim Results = System.Management.Automation.PowerShell.Create.AddScript(Command).AddCommand("out-String").Invoke(Of String)()
			For Each Result In Results
				_Result.AppendLine(Result)
			Next
			RaiseEvent OutputChanged(_Result.ToString)
			RaiseEvent RunFinished()
		End Sub

		Public Event OutputChanged(ByVal Output As String) Implements IRun.OutputChanged

		Public Event RunFinished() Implements IRun.RunFinished
	End Class
End Namespace```
Which is pretty much the same as what we already had in out previous example.
  
Then the async one.

```vbnet
Imports System.Management.Automation.Runspaces
Imports System.Management.Automation

Namespace PowerShell
	Public Class RunASync
		Implements IRun


		Private _RunSpace As Runspace
		Private _PipeLine As Pipeline
		Private WithEvents _OutPut As PipelineReader(Of PSObject)

		Public Event OutPutChanged(ByVal Output As String) Implements IRun.OutputChanged

		Public Sub New()
			_RunSpace = RunspaceFactory.CreateRunspace
			_RunSpace.Open()
		End Sub

		Public Sub Run(ByVal Command As String) Implements IRun.Run
			_PipeLine = _RunSpace.CreatePipeline(Command)
			_PipeLine.Input.Close()
			_OutPut = _PipeLine.Output
			_PipeLine.InvokeAsync()
		End Sub

		Private Sub _Output_DataReady(ByVal sender As Object, ByVal e As System.EventArgs) Handles _OutPut.DataReady
			Dim data = _PipeLine.Output.NonBlockingRead()
			If data.Count &gt; 0 Then
				For Each d In data
					RaiseEvent OutPutChanged(d.ToString & Environment.NewLine)
				Next
			End If
			If _PipeLine.Output.EndOfPipeline Then
				RaiseEvent RunFinished()
			End If
		End Sub

		Public Event RunFinished() Implements IRun.RunFinished
	End Class
End Namespace```
It is pretty simple, make a runspace via the factory. Make a pipeline and hand it your command. Then close the input (very important). And Invoke the command Async. You can then handle the dataready event from the output. Do a NonBlockingRead from the Output and raise an event. When your are at the end of the pipeline you can also send an event to say you are done. Easy as pie.

The codebehind of the form now looks like this.

```vbnet
Imports WindowsApplication2.PowerShell

Public Class frmPowershell

	Private WithEvents _Run As IRun
	Private _RunSync As IRun
	Private _RunASync As IRun

	Public Sub New()

		' This call is required by the designer.
		InitializeComponent()

		' Add any initialization after the InitializeComponent() call.
		_RunSync = New RunSync
		_RunASync = New RunASync
	End Sub

	Private Sub Clear_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btnClear.Click
		txtResult.Text = ""
	End Sub

	Private Sub Run_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btnRun.Click
		Run(_RunSync)
	End Sub

	Private Sub Run(ByVal RunType As IRun)
		btnRun.Enabled = False
		btnRunAsync.Enabled = False
		_Run = RunType
		_Run.Run(Me.txtCommand.Text)
	End Sub

	Private Delegate Sub RunCompleted(ByVal Result As String)

	Private Sub _Run_OutputChanged(ByVal Output As String) Handles _Run.OutputChanged
		If Me.InvokeRequired Then
			Me.Invoke(New RunCompleted(AddressOf _Run_OutputChanged), New Object() {Output})
		Else
			Me.txtResult.AppendText(Output)
		End If
	End Sub

	Private Sub btnForLoop_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btnForLoop.Click
		Me.txtCommand.AppendText("for($x = 0; $x -le 10; $x++)" & Environment.NewLine)
		Me.txtCommand.AppendText("{" & Environment.NewLine)
		Me.txtCommand.AppendText("Write ""Child Task $x"" ;" & Environment.NewLine)
		Me.txtCommand.AppendText("Sleep -Milliseconds 300;" & Environment.NewLine)
		Me.txtCommand.AppendText("}" & Environment.NewLine)
	End Sub

	Private Delegate Sub RunFinishedDelegate()

	Private Sub _Run_RunFinished() Handles _Run.RunFinished
		If Me.InvokeRequired Then
			Me.Invoke(New RunFinishedDelegate(AddressOf _Run_RunFinished), Nothing)
		Else
			Me.btnRun.Enabled = True
			Me.btnRunAsync.Enabled = True
		End If
	End Sub

	Private Sub btnRunAsync_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles btnRunAsync.Click
		Run(_RunASync)
	End Sub
End Class```
You can now click the two buttons to notice the difference between sync and async. Also notice the insane amount of code you have to use to make updating winforms controls in an async matter (I so hate that). 

I think the next step is to create a profile variable and thus also add an external script to our pipeline. Repeat after me, this is fun.

 [1]: /index.php/DesktopDev/MSTech/powershell-in-your-vb-net