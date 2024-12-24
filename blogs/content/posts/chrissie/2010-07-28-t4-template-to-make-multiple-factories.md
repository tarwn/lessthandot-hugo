---
title: T4 template to make multiple factories
author: Christiaan Baes (chrissie1)
type: post
date: 2010-07-28T09:31:26+00:00
ID: 856
url: /index.php/desktopdev/mstech/t4-template-to-make-multiple-factories/
views:
  - 4053
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - multiple output
  - t4
  - vb.net

---
## Introduction

A few days ago I made a post on [how to make a factory with T4 templates][1]. That version only made one factory for one namespace. So it was pretty limiting. But as you could see, in that example, we had 2 namespaces, namely Test and Test2.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/T4/T4.png" alt="" title="" width="177" height="179" />
</div>

We need a factory for both those namespaces. We can either copy/paste or change our template to produce more files. After all, these files are nearly the same.

## The code

The code for this is pretty simple, once you know how. And here it is.

```
&lt;#@ template language="C#" #&gt;
&lt;#@ output extension="vb" #&gt; 
&lt;#@ VolatileAssembly processor="T4Toolbox.VolatileAssemblyProcessor" name="$(SolutionDir)/ConsoleApplication1/bin/Debug/ConsoleApplication1.exe" #&gt;
&lt;#@ assembly name="System.Core" #&gt;
&lt;#@ import namespace="System" #&gt;
&lt;#@ import namespace="System.Linq" #&gt;
&lt;#@ import namespace="System.Reflection" #&gt;
&lt;#@ import namespace="ConsoleApplication1.Test" #&gt;
&lt;#@ import namespace="System.Collections.Generic" #&gt;
&lt;# 
var namespaces = new List&lt;String&gt;();
namespaces.Add("Test");
namespaces.Add("Test2");

string relativeOutputFilePath = "";

foreach(String _namespace in namespaces)
{ #&gt; ' **************************************
' Generated code, please do not change
' This file was last generated on &lt;#= DateTime.Now #&gt;
' **************************************

Namespace ConsoleApplication1.&lt;#= _namespace #&gt;

    ''' &lt;summary&gt;
    ''' 
    ''' &lt;/summary&gt;
    ''' &lt;remarks&gt;&lt;/remarks&gt;
    &lt;CLSCompliant(True)&gt; _
    Public Interface IFactory

#Region "Methods"

        &lt;# var ty = typeof(Interface3);
	        var assem = ty.Assembly;
            var types = from e in assem.GetTypes() where e.IsInterface && e.Namespace == "ConsoleApplication1." + _namespace select e;
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

End Namespace
&lt;# 
if(!System.IO.Directory.Exists(".../ConsoleApplication1/" + _namespace + "/Factory"))
{
	System.IO.Directory.CreateDirectory(".../ConsoleApplication1/" + _namespace + "/Factory");
}
relativeOutputFilePath = ".../ConsoleApplication1/" + _namespace + "/Factory/IFactory.cs";
WriteTemplateOutputToFile(relativeOutputFilePath);
}
 #&gt;
&lt;#+
    void WriteTemplateOutputToFile(string relativeOutputFilePath)
    {
        string outputFilePath = relativeOutputFilePath;
        System.IO.File.WriteAllText(outputFilePath, this.GenerationEnvironment.ToString());
		this.GenerationEnvironment.Remove(0, this.GenerationEnvironment.Length);
    }

#&gt;```
As you can see, I added a List with namespaces, I could have just gotten them from the assembly as well. And then I have a for each which makes a template for each of those namespaces. In the end of that loop, I call WriteTemplateOutputToFile with the desired path. And now comes the important bit. 

```
void WriteTemplateOutputToFile(string relativeOutputFilePath)
    {
        string outputFilePath = relativeOutputFilePath;
        System.IO.File.WriteAllText(outputFilePath, this.GenerationEnvironment.ToString());
		this.GenerationEnvironment.Remove(0, this.GenerationEnvironment.Length);
    }```
The bit of code above writes the generated code to the filesystem and clears the Generated code so that you get a blank slate to generate the next file. I also added a comment at the top of the file to warn users not to change the file since it is generated and changes will get lost when regenerated.

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DesktopDev/T4/T4_4.png" alt="" title="" width="189" height="268" />
</div>

## Conclusion

Why write code if you can have it generated? Lazy people make good programmers and even better DBA&#8217;s.
  
Adding a warning to your generated code is also important so that other people don&#8217;t change it just to find out they their changes don&#8217;t stick.

## Resources

  * [How to generate multiple outputs from single T4 template][2] by [Oleg Sych][3]
  * [Multiple outputs from T4 made easy – revisited][4] by [Damian Guard][5]
  * [StackOverflow answer][6] to question [T4 template to create \*multiple\* html (for example) output files per table from database schema][7] by [Michael Maddox][8]

 [1]: /index.php/DesktopDev/MSTech/t4-template-to-make-a-factory-class
 [2]: http://www.olegsych.com/2008/03/how-to-generate-multiple-outputs-from-single-t4-template/
 [3]: http://www.olegsych.com
 [4]: http://damieng.com/blog/2009/11/06/multiple-outputs-from-t4-made-easy-revisited
 [5]: http://damieng.com/blog
 [6]: http://stackoverflow.com/questions/2223421/t4-template-to-create-multiple-html-for-example-output-files-per-table-from-d/3070406#3070406
 [7]: http://stackoverflow.com/questions/2223421/t4-template-to-create-multiple-html-for-example-output-files-per-table-from-d
 [8]: http://stackoverflow.com/users/12712/michael-maddox