---
title: StructureMap, Windows Forms, User controls and BuildUp
author: Christiaan Baes (chrissie1)
type: post
date: 2010-02-24T04:33:19+00:00
ID: 708
url: /index.php/desktopdev/mstech/structuremap-windows-forms-user-controls/
views:
  - 4533
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - buildup
  - structuremap

---
For my usercontrols I tend to use the StructureMap BuildUp feature. I will leave it to [the big man himself][1] to [introduce you][2] to this feature.

I use property injection in this case because the designer doesn&#8217;t like usercontrols that have constructors that are not empty, since it will try to execute the code in that constructor. So we revert to property injection and the BuildUp feature. I did say revert because I don&#8217;t think this is the best solution. But you have to be careful when using this. 

For example when you have this.

```vbnet
Public Class Gridje
   Inherits DataGridView

   Private _Controller as IController

   Public Sub New()
      StructureMap.ObjectFactory.BuildUp(Me)
   End Sub

   Public Property Controller as IController
      Get
         Return _Controller
      End Get
      Set(ByVal value As IController)
         _Controller = value
      End Set
   End Property
End Class```
I&#8217;ll leave the rest to your imagination.

The biggest problem you will face is that the designer will see your property and set it to null/nothing all by itself. Just add the above DataGridView to a Form and see that in the designer code you will get something like this.

```vbnet
Me.Gridje1.Controller = Nothing```
This is much fun since the BuildUp code will inject the correct object for you and the designer code which is executed after the constructor code will set it back to null and will leave you wondering why your code doesn&#8217;t work any more and who is the culprit. Of course if you have [Resharper][3] then you can use the function find usages. 

To prevent the designer from doing this, you have to decorate these properties with an attribute (or two).

```vbnet
&lt;System.ComponentModel.Browsable(False)&gt; _        &lt;System.ComponentModel.DesignerSerializationVisibility(System.ComponentModel.DesignerSerializationVisibility.Hidden)&gt; _
        ```
I use the two above. I&#8217;m pretty sure the DesignerSerializationVisibility alone will be sufficient but I like the Browsable one since it doesn&#8217;t need to be Browsable anyway.

There you have it, problem solved.

BTW I wrote a wrapper for registering my objects in StructureMap so that every object I register is also use in BuildUp that way using BuildUp becomes a no-brainer.

 [1]: http://codebetter.com/blogs/jeremy.miller/default.aspx
 [2]: http://codebetter.com/blogs/jeremy.miller/archive/2009/01/16/quot-buildup-quot-existing-objects-with-structuremap.aspx
 [3]: http://www.jetbrains.com/resharper/