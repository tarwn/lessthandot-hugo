---
title: Adding a menuItem to the NArrangeExtension.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-30T11:18:10+00:00
ID: 773
url: /index.php/desktopdev/mstech/adding-a-menuitem-to-the-narrangeextensi/
views:
  - 2143
categories:
  - Microsoft Technologies
tags:
  - extension
  - narrange
  - vb.net
  - vs2010

---
In my [previous blogpost][1] I showed you how to make an extension for VS2010 which adds a menuitem to the contextmenu of a project in the solution explorer.

Now, I want to also add a ContextmenuItem to the ProjectItems. You might think this should be pretty painless. Well, think again.

First of all we go back to our vsct file and add a group to our groups node. Because it&#8217;s the groupnode that decides where the menuitem will show up.

```xml
&lt;Group guid="guidNArrangeFileExtensionCmdSet" id="MyMenuFileGroup" priority="0x0600"&gt;
			&lt;Parent guid="guidSHLMainMenu" id="IDM_VS_CTXT_ITEMNODE"/&gt;
		&lt;/Group&gt;```
As you can see, I used a new guid and set the Parent id to IDM\_VS\_CTXT_ITEMNODE. 

Now that Guid (guidNArrangeFileExtensionCmdSet) comes from somewhere. When you follow the wizard it magically creates one for you in the Guids.vb file. And you have to add your new Guid in there, too.

```vbnet
Imports System

Class GuidList
    Private Sub New()
    End Sub

    Public Const guidNArrangeExtensionPkgString As String = "46d127d3-1425-4299-8163-a8e36a65c725"
    Public Const guidNArrangeExtensionCmdSetString As String = "4ce0ccbd-0c52-40ea-b4dd-158414a64460"
    Public Const guidNArrangeFileExtensionCmdSetString As String = "4de0ccbd-0c52-40ea-b4dd-158414a64460"

    Public Shared ReadOnly guidNArrangeExtensionCmdSet As New Guid(guidNArrangeExtensionCmdSetString)
    Public Shared ReadOnly guidNArrangeFileExtensionCmdSet As New Guid(guidNArrangeFileExtensionCmdSetString)
End Class```
You can see I added the guidNArrangeFileExtensionCmdSetString and the guidNArrangeFileExtensionCmdSet bits to add. All the rest was there already.

Then we go back to the vsct file and add a button to the Buttons node.

```xml
&lt;Button guid="guidNArrangeFileExtensionCmdSet" id="cmdNarrangeFile" priority="0x0100" type="Button"&gt;
			&lt;Parent guid="guidNArrangeFileExtensionCmdSet" id="MyMenuFileGroup" /&gt;
			&lt;Strings&gt;
				&lt;CommandName&gt;cmdNarrangeFile&lt;/CommandName&gt;
				&lt;ButtonText&gt;Narrange File&lt;/ButtonText&gt;
			&lt;/Strings&gt;
		&lt;/Button&gt;```
similar as our previous button but a different Parent guid and CommandName. That command name isn&#8217;t magic either. You have to add it to the PkgCmdID.vb file.

```vbnet
' PkgCmdID.vb
Imports System

Class PkgCmdIDList
    Private Sub New()
    End Sub

    Public Const cmdNarrangeProject As UInteger = &H100
    Public Const cmdNarrangeFile As UInteger = &H101


End Class```
I also gave that a number.

When you have this all set up you can already try to run this. The menuitem will show up but won&#8217;t do a thing.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension10.png" alt="" title="" width="469" height="291" />
</div>

But now we have to tell our code to do something different when that button is clicked.

I want it to do this.

```vbnet
Private Sub NarrangeFile(ByVal sender As Object, ByVal e As EventArgs)
        ' Show a Message Box to prove we were here
        Dim dte As EnvDTE.DTE = CType(GetService(GetType(EnvDTE.DTE)), EnvDTE.DTE)
        Dim dte2 As EnvDTE80.DTE2 = CType(dte, EnvDTE80.DTE2)
        Dim solexplorer As UIHierarchy
        solexplorer = dte2.ToolWindows.SolutionExplorer
        For Each i As UIHierarchyItem In solexplorer.SelectedItems
            Dim project As EnvDTE.ProjectItem = i.Object
            Dim s = project.FileNames(0)
            System.Diagnostics.Process.Start("narrange-console", """" & s & """").WaitForExit(10000)
        Next```
You just add that piece of code to the Package.vb (endswith) file. and change the Initialize method. You should already have something like this in there

```vbnet
Dim NarrangeProjectId As New CommandID(GuidList.guidNArrangeExtensionCmdSet, CInt(PkgCmdIDList.cmdNarrangeProject))
            Dim NarrangeProjectMenuItem As New MenuCommand(New EventHandler(AddressOf MenuItemCallBack), NarrangeProjectId)
            mcs.AddCommand(NarrangeProjectMenuItem)
            ```
Change the MenuItemCallBack methodname to NArrangeProject and change the above code to 

```vbnet
Dim NarrangeProjectId As New CommandID(GuidList.guidNArrangeExtensionCmdSet, CInt(PkgCmdIDList.cmdNarrangeProject))
            Dim NarrangeProjectMenuItem As New MenuCommand(New EventHandler(AddressOf NarrangeProject), NarrangeProjectId)
            Dim NarrangeFileId As New CommandID(GuidList.guidNArrangeFileExtensionCmdSet, CInt(PkgCmdIDList.cmdNarrangeFile))
            Dim NarrangeFileMenuItem As New MenuCommand(New EventHandler(AddressOf NarrangeFile), NarrangeFileId)
            mcs.AddCommand(NarrangeProjectMenuItem)
            mcs.AddCommand(NarrangeFileMenuItem)```
And you will have your code hooked up with the menuitems. The above code is ugly as hell but it works. And it was fun finding out where everything lives and what it does. Finding out the hard way is so much more fun than reading the manual. If you can ever find the manual let me know.

 [1]: /index.php/DesktopDev/MSTech/my-first-vs2010-extension