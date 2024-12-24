---
title: 'Resharper plugin: rename class to filename'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-09-16T11:51:00+00:00
ID: 1724
excerpt: Just for the hell of it I wrote a resharper plugin to rename the typename to match the filename.
url: /index.php/desktopdev/mstech/resharper-plugin-rename-class-to/
views:
  - 6170
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - resharper 7

---
## Introduction

We all know [Resharper][1] and how it makes our lives as Visual studio users so much better. 

But alas, sometimes there is this one little thing missing.

If you used resharper you will have noticed that if your filename and typename don&#8217;t match up it shows you the &#8220;rename file to match typename&#8221; quick fix. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperPlugin1.png?mtime=1347802545"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperPlugin1.png?mtime=1347802545" width="322" height="133" /></a>
</div>

But not the other way around &#8220;Rename type to macth filename&#8221;. Which is odd because sometimes that can be handy. In case you edited the filename and forgot to edit the classname to match.

## How I did it

First of all you need Resharper 7 and you need the resharper 7 SDK. Both can be found on the [Resharper download page][2].

Then you need to create a new Resharper 7.0 plugin project. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperPlugin2.png?mtime=1347802747"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperPlugin2.png?mtime=1347802747" width="955" height="660" /></a>
</div>

You then need to inherit from ContextActionBase.

```vbnet
Imports JetBrains.ReSharper.Feature.Services.Bulbs
Imports JetBrains.ReSharper.Intentions.Extensibility
Imports JetBrains.ReSharper.Feature.Services.VB.Bulbs
Imports JetBrains.Util
Imports JetBrains.ProjectModel
Imports JetBrains.Application.Progress
Imports JetBrains.TextControl

&lt;ContextAction(Description:="ClassNameToFileNameQuickFix",
               Group:="VB",
               Name:="ClassNameToFileNameQuickFix")&gt;
Public Class ClassNameToFileNameQuickFixAction
    Inherits ContextActionBase

    Private _provider As IVBContextActionDataProvider
    
    Public Sub New(dataProvider As IVBContextActionDataProvider)
        _provider = dataProvider
    End Sub

    Protected Overrides Function ExecutePsiTransaction(ByVal solution As ISolution, ByVal progress As IProgressIndicator) As System.Action(Of ITextControl)
        Dim literal = _provider.GetSelectedElement(Of JetBrains.ReSharper.Psi.Tree.ITypeDeclaration)(True, True)
        If (literal IsNot Nothing) Then
            literal.SetName(literal.GetSourceFile().Name.Substring(0, literal.GetSourceFile().Name.Length - 3))
        End If
        Return Nothing
    End Function

    Public Overrides ReadOnly Property Text As String
        Get
            Return "Rename class to match filename"
        End Get
    End Property

    Public Overrides Function IsAvailable(ByVal cache As IUserDataHolder) As Boolean
        Dim literal = _provider.GetSelectedElement(Of JetBrains.ReSharper.Psi.Tree.ITypeDeclaration)(True, True)
        If (literal IsNot Nothing AndAlso Not literal.DeclaredName & ".vb" = literal.GetSourceFile().Name) Then
            Return True
        End If
        Return False
    End Function

End Class
```
The Property you see is the text that will be shown when the quickfix is available. The code in the Isavailable method tells it when to show the quickfix and the ExecutePsiTransaction tells it what to do when the user clicks on that quickfix.

So now I just have to build the project and copy the dll over to the Plugins folder of resharper %programFiles%JetBrainsReSharperv7.0BinPlugins. Which you may have to create because in my case it did not exists.

And now after restarting VS I see this when I Alt Enter on the classname when filename and classname don&#8217;t match. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperPlugin3.png?mtime=1347803232"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperPlugin3.png?mtime=1347803232" width="305" height="126" /></a>
</div>

And when I select my new plugin I get this as the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperPlugin4.png?mtime=1347803338"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/resharper/ResharperPlugin4.png?mtime=1347803338" width="225" height="117" /></a>
</div>

Woohoo, it works.

## Conclusion

This was a proof of concept because I&#8217;m sure this won&#8217;t always work and it needs a C# version but I will add that later, should be simple enough.

 [1]: http://www.jetbrains.com/resharper/
 [2]: http://www.jetbrains.com/resharper/download/index.html