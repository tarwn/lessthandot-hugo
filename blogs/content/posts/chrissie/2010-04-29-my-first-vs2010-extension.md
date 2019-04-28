---
title: My first VS2010 Extension
author: Christiaan Baes (chrissie1)
type: post
date: 2010-04-29T05:14:30+00:00
ID: 771
url: /index.php/desktopdev/mstech/my-first-vs2010-extension/
views:
  - 5921
categories:
  - Microsoft Technologies
  - VB.NET
tags:
  - extension
  - narrange
  - vb.net
  - vs2010

---
I felt the need to write a VS2010 Extension. And I decided NArrange needed some love and attention from me. NArrange works great but it needs to be integrated with VS2010 instead of me having to [create the context menu items from scratch][1].

So I started by downloading the [VS2010 SDK][2]. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/extension5.png" alt="" title="" width="382" height="84" />
</div>

This adds a category to our new project dialog and it adds several new templates to choose from in that category.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension1.png" alt="" title="" width="515" height="468" />
</div>

I went for the Visual studio Package and named it NArrangeExtension. And now it&#8217;s time to follow the wizard.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension2.png" alt="" title="" width="955" height="660" />
</div>

Then it is just being friendly. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension3.png" alt="" title="" width="516" height="394" />
</div>

I choose to make it Visual Basic of course (why would I go for less?). 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension4.png" alt="" title="" width="516" height="394" />
</div>

I did not fill in any information since I know what I&#8217;m doing.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension5.png" alt="" title="" width="516" 
height="394" />
</div>

I wanted to make a menu so I choose that.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension6.png" alt="" title="" width="516" height="394" />
</div>

I changed the command name and Id so they would me meaningful to me and the rest of the world.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension7.png" alt="" title="" width="516" height="394" />
</div>

And I choose to create an integration test project and unit test project because it sounds cool, but I have not yet used them.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension8.png" alt="" title="" width="516" height="394" />
</div>

Now on to the more important bits. The programming. Let me say that documentation is not easy to find, but it is out there in some form or the other. 

First you should open your vsct file. This is an xml file which helps you configure your menu options. 

The first important line in here is this.

```xml
&lt;!--This header contains the command ids for the menus provided by the shell. --&gt;
  &lt;Extern href="vsshlids.h"/&gt;
```
Do a search on your system and find that vsshlids.h file. It will help a little. It contains all the options you can set. But as always, it contains lots of things with little documentation. But at least you now have something you can google. 

I wanted to change the location of my menuitem to be in the
  
contextmenu when I right-click on the project in my solution explorer. 

```xml
&lt;Group guid="guidNArrangeExtension2CmdSet" id="MyMenuGroup" priority="0x0600"&gt;
        &lt;Parent guid="guidSHLMainMenu" id="IDM_VS_MENU_TOOLS"/&gt;
      &lt;/Group&gt;```
The above line says that it will create the menuoption in the tools menu, this is the default. But [Martin Tracy&#8217;s Blog][3] told me I needed to change the <code class="codespan">id="IDM_VS_MENU_TOOLS"</code> to this <code class="codespan">id="IDM_VS_CTXT_PROJNODE"</code>. And now the menu item will be shown in the context menu, easy isn&#8217;t it?

You then go to the Buttons element and look for this.

```xml
&lt;Button guid="guidNArrangeExtension2CmdSet" id="cmdNArrangeProject" priority="0x0100" type="Button"&gt;
        &lt;Parent guid="guidNArrangeExtension2CmdSet" id="MyMenuGroup" /&gt;
        &lt;Icon guid="guidImages" id="bmpPic1" /&gt;
        &lt;Strings&gt;
          &lt;CommandName&gt;cmdNArrangeProject&lt;/CommandName&gt;
          &lt;ButtonText&gt;NArrange Project&lt;/ButtonText&gt;
        &lt;/Strings&gt;
      &lt;/Button&gt;```
You can and should remove the Icon tag. Or you could change it into something useful instead.

And we have now setup the menuitem.

If you run the Extension it will magically open a Visual studio window and it will even attach the debugger for you. How cool is that! 

You then open a solution and right click on a project.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/Extensions/NArrangeExtension9.png" alt="" title="" width="440" height="317" />
</div>

IT WORKS!

But no need to click it. It will just show a messagebox. Which is not what we want.

You now go to the file with Package.vb in the name. There you will find a MenuItemCallback method. Delete the default code.

And enter this code instead.

```vbnet
Dim dte As EnvDTE.DTE = CType(GetService(GetType(EnvDTE.DTE)), EnvDTE.DTE)
        Dim dte2 As EnvDTE80.DTE2 = CType(dte, EnvDTE80.DTE2)
        Dim solexplorer As UIHierarchy
        solexplorer = dte2.ToolWindows.SolutionExplorer
        For Each i As UIHierarchyItem In solexplorer.SelectedItems
            Dim project As Project = i.Object
            Dim s = project.FullName
            System.Diagnostics.Process.Start("narrange-console", """" & s & """").WaitForExit(10000)
        Next```
For this to work you will have to have NArrange installed on your system. You can [download it from sourceforge][4]. In my final project, I want to include the NArrange source code. But I will ask for the permission of the creator first.

The hard part was finding how to get the projectname. I thought this was going to be easy but apparently I still needed some DTE for this. And the process also needed all those nasty double quotes. 

And that&#8217;s it. In the next version I will add a contextmenuitem for projectitems which was also a little challenging, but only a very little challenge.

 [1]: /index.php/All/?p=547
 [2]: http://www.microsoft.com/downloads/en/confirmation.aspx?familyId=47305cf4-2bea-43c0-91cd-1b853602dcc5&displayLang=en
 [3]: http://blogs.msdn.com/martintracy/archive/2006/05/16/599057.aspx
 [4]: http://sourceforge.net/projects/narrange/files/