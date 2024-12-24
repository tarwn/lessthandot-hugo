---
title: T4 template to make a factory class.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-07-24T15:01:26+00:00
ID: 853
url: /index.php/desktopdev/mstech/t4-template-to-make-a-factory-class/
views:
  - 4617
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - t4
  - template
  - vb.net
  - visual studio

---
## Introduction

Some jobs are tedious and errorprone. To avoid the errors we should unittest, but to avoid the tediousness we can make use of code generation. One such code generation tool is T4. It is available in Visual studio so why not use it? Well I can think of a few reasons. No intellisense no code coloring and no refactoring. But I guess we will have to learn to live with that. Or use a third party editor like the one from [Clarius consulting][1]. So I thought code generation would be a good fit for some of my factory classes I have. 

## The goal

Well that is simple I want to create this file automagicaly.

```vbnet
Imports ConsoleApplication1.Test

Namespace ConsoleApplication1.T4

    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
    Public Interface IFactory

#Region "Methods"

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Function Interface3() As IInterface3

        ''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
        Function Interface4() As IInterface4

#End Region

    End Interface

End Namespace```
The point is that it should create a Function for every Interface I have in a certain namespace. 

## Getting the interfaces

First problem to solve was how to easily get the interfaces in a nice list.

I made a ConsoleApplication to test this. First I added a few interfaces in 2 different namespace and then I created a program to find the interfaces in the namespace I want.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/T4/T4.png" alt="" title="" width="177" height="179" />
</div>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Reflection;
using System.Text;
using ConsoleApplication1.Test2;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            var ty = typeof(Interface1);
            var assem = ty.Assembly;
            var types = from e in assem.GetTypes() where e.IsInterface && e.Namespace == "ConsoleApplication1.Test" select e;
            if( types.Count() &gt; 0)
            {
                foreach(var cls in types)
                {
                    Console.WriteLine("Function {0}() As {1}",cls.Name.Substring(1),cls.Name);
                }
            }
            Console.ReadLine();
        }
    }
}
```
Yes that is C#. And why is it C#? Because I decided to write T4 templates in C#. To keep the generated code apart from the generating code. You could also write the generating code in VB.Net if you like.
  
So we have this covered. Seemed simple enough. 

## The template

And now came the tricky part, writing the template.

First of all we create a file called IFactory.tt. Visual studio will give you a warning if you do that, but it is safe to ignore that warning, just pretend that you know what you are doing.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/T4/T4_1.png" alt="" title="" width="548" height="199" />
</div>

Then we add the generating language. and we tell it to output our file with a .vb extension.

```
&lt;#@ template language="C#" #&gt;
&lt;#@ output extension="vb" #&gt; 
```
Now for the code.

```
Imports ConsoleApplication1.Test

Namespace ConsoleApplication1.T4

    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
    Public Interface IFactory

#Region "Methods"

        &lt;# var ty = typeof(IInterface3);
	        var assem = ty.Assembly;
            var types = from e in assem.GetTypes() where e.IsInterface && e.Namespace == "ConsoleApplication1.Test" select e;
            if( types.Count() &gt; 0)
            {
				foreach(var cls in types)
                {
					var _FunctionName = cls.Name.Substring(1);
					#&gt;
		''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
		Function &lt;#=_FunctionName#&gt;() As &lt;#=cls.Name#&gt;

					&lt;#
                }
            }
			#&gt;
#End Region

	End Interface

End Namespace```
Text between <# #> tags is our code text between <#= #> tags is text that will be output to the file. Text between <#@ #> tags are directives.

The above will fail miserably and Google failed me too. 

You will get 2 errors out of the above.

> Compiling transformation: The type or namespace name &#8216;IFactory&#8217; could not be found (are you missing a using directive or an assembly reference?) 

and this one

> Compiling transformation: Could not find an implementation of the query pattern for source type &#8216;System.Type[]&#8217;. &#8216;Where&#8217; not found. Are you missing a reference to &#8216;System.Core.dll&#8217; or a using directive for &#8216;System.Linq&#8217;? 

Clearly it is complaining that you need to set references and imports.

However it is not always clear to me when you need to this and for which libraries. I guess we just wait for an error and then add it.

In my case I needed to add these.

```
&lt;#@ assembly name="C:..binx86DebugConsoleApplication1.exe" #&gt;
&lt;#@ assembly name="System.Core" #&gt;
&lt;#@ import namespace="System" #&gt;
&lt;#@ import namespace="System.Linq" #&gt;
&lt;#@ import namespace="System.Reflection" #&gt;
&lt;#@ import namespace="ConsoleApplication1.Test" #&gt;
```
The directives with assembly are actualy what you would do when you do Add reference for your project. If it is a system assembly then you can just use the short name like I did for System.Core if you want to reference one of you project then you need to add the fullpath and filename. like I did for my project. If you then have all the references you need you can add import directives. And then it all just works.

So the final code would then be.

```
&lt;#@ assembly name="C:..binx86DebugConsoleApplication1.exe" #&gt;
&lt;#@ assembly name="System.Core" #&gt;
&lt;#@ import namespace="System" #&gt;
&lt;#@ import namespace="System.Linq" #&gt;
&lt;#@ import namespace="System.Reflection" #&gt;
&lt;#@ import namespace="ConsoleApplication1.Test" #&gt;
Imports ConsoleApplication1.Test

Namespace ConsoleApplication1.T4

    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
    Public Interface IFactory

#Region "Methods"

        &lt;# var ty = typeof(IInterface3);
	        var assem = ty.Assembly;
            var types = from e in assem.GetTypes() where e.IsInterface && e.Namespace == "ConsoleApplication1.Test" select e;
            if( types.Count() &gt; 0)
            {
				foreach(var cls in types)
                {
					var _FunctionName = cls.Name.Substring(1);
					#&gt;
		''' &lt;summary&gt;
        ''' 
        ''' &lt;/summary&gt;
        ''' &lt;returns&gt;&lt;/returns&gt;
        ''' &lt;remarks&gt;&lt;/remarks&gt;
		Function &lt;#=_FunctionName#&gt;() As &lt;#=cls.Name#&gt;

					&lt;#
                }
            }
			#&gt;
#End Region

	End Interface

End Namespace```
The tt will now have a vb file attached to it.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/T4/T4_3.png" alt="" title="" width="194" height="56" />
</div>

And that will then look like this.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/T4/T4_2.png" alt="" title="" width="647" height="809" />
</div>

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/T4/T4_3.png" alt="" title="" width="194" height="56" />
</div>

All ready for use. Now I can just add an interface and the factory will.

<span class="MT_red">Warning</span>

The above code works, but as soon as you build your project it wiil complain to say that the exe in the bin folder is blocked. This is a well known problem. It has to with the fact that the template engine does not release the AppDomain immediatly, but it caches it. There is however a nice solution to this problem. And it is called the T4 toolbox and the VolatileAssembly. You just need to download and install the Toolbox then you need to swap the assembly directive to this.

You can read more abot this problem in these locations.

<#@ VolatileAssembly processor="T4Toolbox.VolatileAssemblyProcessor" name="$(SolutionDir)/ConsoleApplication1/bin/Debug/ConsoleApplication1.exe" #>

  * [Understanding T4: <#@ assembly #> directive][2] by [Oleg Sych][3]
  * [Working with the T4][4] by [Siderite][5]

## Conclusion

Code generation is a powerful tool that can take a lot of work out of your hands. But it can also become a maintenance nightmare if you aren&#8217;t careful. I learned a lot while making this post. And all this because someone on [Stackoverflow][6] asked [how to add the namespace to a class template for VB.Net][7].

## Resources

  * [T4 (Text Template Transformation Toolkit) Code Generation &#8211; Best Kept Visual Studio Secret][8] by [Scott Hanselman][9]
  * [Code generation using T4 Templates][10] by [DotNetThoughts][11]
  * [Everything about T4][12] by [Oleg Sych][13]
  * [Walkthrough: Creating and Running Text Templates][14] on MSDN
  * [How-To Q&A: How Can I Automate Code Without Resorting to Heavy Code-Generation Techniques?][15] by Kathleen Dollard

 [1]: http://www.t4editor.net/downloads.html
 [2]: http://www.olegsych.com/2008/02/t4-assembly-directive/
 [3]: http://www.olegsych.com/
 [4]: http://siderite.blogspot.com/2010/06/working-with-t4.html
 [5]: http://siderite.blogspot.com
 [6]: http://stackoverflow.com
 [7]: http://stackoverflow.com/questions/3317141/vb-net-automatically-add-namespace-when-adding-new-item
 [8]: http://www.hanselman.com/blog/T4TextTemplateTransformationToolkitCodeGenerationBestKeptVisualStudioSecret.aspx
 [9]: http://www.hanselman.com
 [10]: http://www.dotnetthoughts.net/2009/07/31/code-generation-using-t4-templates/
 [11]: http://www.dotnetthoughts.net
 [12]: http://www.olegsych.com/tag/t4/
 [13]: http://www.olegsych.com
 [14]: http://msdn.microsoft.com/en-us/library/bb126484%28VS.90%29.aspx
 [15]: http://visualstudiomagazine.com/articles/2010/04/01/scaled-down-code-generation.aspx