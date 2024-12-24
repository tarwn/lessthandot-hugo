---
title: 'Arranging your VB.Net and C# code with NArrange'
author: Christiaan Baes (chrissie1)
type: post
date: 2009-07-31T04:52:47+00:00
ID: 527
excerpt: Explains how to install and use NArrange and how to incorporate it in Visual studio to have faster and eassier acces to it.
url: /index.php/desktopdev/mstech/arranging-your-vb-net-and-c-code-with-na/
views:
  - 19915
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - narrange
  - vb.net
  - visual studio

---
Today I discovered a nice little tool for Visual studio called [NArrange][1]. This is what it does according to their website.

> * Reduces the amount of time developers spend arranging members within code files. With NArrange, you don&#8217;t need to worry about where you place a new member definition in a class&#8230; just type away and run NArrange prior to compilation. After formatting, the new member will be automatically moved to the appropriate location in the source file.
      
> * Helps enforce coding style standards
      
> * When used as part of check-in procedures, NArrange can help reduce source code repository conflicts.
      
> * NArrange can automatically group similar code members into predefined region blocks, if supported by the language (C# and VB.Net).
      
> * Reduces the amount of time spent searching for specific members in a code file. Through standard arrangement of source code files, every member of the team will know exactly where in a file to look for private fields, constructors, etc.
      
> * Flexibility &#8211; NArrange allows you to configure how members are organized (grouping, sorting, regions, etc.)
      
> * Sort Usings 

Sounds promising since I already organise my code that way. I find it makes it easier to find things in my code. Of course, my source files are never very big (if a class file has more then a hundred lines of code in it we can call it big and I don&#8217;t have many of those, apart from the designer generated files of course). 

So lets get started and set it up. First we download the msi file from the site and do the wizard thing. 

Then we set it up in external tools. 

Go to Tools -> Extern tools&#8230; click Add and fill in the arguments and initial directory and the command. like in the image.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/narrange/narrange1.png" alt="" title="" width="461" height="448" />
</div>

The tool I created here is to do an NArrange on a single source file with a backup of the original (not sure I really need that since I have source control but better safe than finding out you&#8217;re a father at the age of 60) 

So now we can try this out by choosing a source file and then clicking on Tools -> NArrange.

Of course this works but it is a drag I would rather right-click on a file and do NArrange. No problem you can set this up.

Go to View -> Toolbars -> Customize&#8230;, check Context menus and see an extra toolbar appear (magic). Now go to Tools -> NArrange (most likely this will be called External Command 4 but you can change that by right-clicking on it. And drag it while holding down the ctrl-key to the Project and Solution Context Menus -> Item

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/narrange/narrange2.png" alt="" title="" width="670" height="284" />
</div>

Then click close on the customize dialog box.

Now you can click on a file and have an NArrange option to click on.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/narrange/narrange3.png" alt="" title="" width="297" height="223" />
</div>

With this as the result in the Output window.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/narrange/narrange4.png" alt="" title="" width="694" height="336" />
</div>

So now I can go in seconds from this.

```vbnet
Namespace Raman.Parameters
    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
    Public Class SavitskySmoothParameter

#Region " Private members "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _DegreeOfSmoothing As DegreeOfSmoothingEnum
#End Region

#Region " Constructors "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;param name="DegreeOfSmoothing"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New(ByVal DegreeOfSmoothing As DegreeOfSmoothingEnum)
            _DegreeOfSmoothing = DegreeOfSmoothing
        End Sub
#End Region

#Region " Enums "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Enum DegreeOfSmoothingEnum
            DegreeOfSmoothing1 = 5
            DegreeOfSmoothing2 = 9
            DegreeOfSmoothing3 = 13
            DegreeOfSmoothing4 = 19
            DegreeOfSmoothing5 = 25
        End Enum
#End Region

#Region " Public properties "
        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public ReadOnly Property DegreeOfSmoothing() As DegreeOfSmoothingEnum
            Get
                Return _DegreeOfSmoothing
            End Get
        End Property
#End Region

    End Class
End Namespace```
to this

```vbnet
Namespace Raman.Parameters

    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
    Public Class SavitskySmoothParameter

        #Region "Fields"

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Private _DegreeOfSmoothing As DegreeOfSmoothingEnum

        #End Region 'Fields

        #Region "Constructors"

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;param name="DegreeOfSmoothing"&gt;&lt;/param&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Sub New(ByVal DegreeOfSmoothing As DegreeOfSmoothingEnum)
            _DegreeOfSmoothing = DegreeOfSmoothing
        End Sub

        #End Region 'Constructors

        #Region "Enumerations"

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public Enum DegreeOfSmoothingEnum
            DegreeOfSmoothing1 = 5
            DegreeOfSmoothing2 = 9
            DegreeOfSmoothing3 = 13
            DegreeOfSmoothing4 = 19
            DegreeOfSmoothing5 = 25
        End Enum

        #End Region 'Enumerations

        #Region "Properties"

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;value&gt;&lt;/value&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Public ReadOnly Property DegreeOfSmoothing() As DegreeOfSmoothingEnum
            Get
                Return _DegreeOfSmoothing
            End Get
        End Property

        #End Region 'Properties

    End Class

End Namespace

```
Pretty cool, I think, and something useful for me. Probably not for everyone.

I can now make one for doing complete projects and even solutions.

 [1]: http://www.narrange.net/