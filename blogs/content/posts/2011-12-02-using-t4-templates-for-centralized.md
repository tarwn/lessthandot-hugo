---
title: Using T4 templates for Centralized Javascript
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-12-02T15:42:00+00:00
ID: 1404
excerpt: In my previous post I mentioned that I was looking for an answer to the age-old question of how to manage common CSS and JavaScript across multiple projects (specifically ASP.Net projects). Using T4 templates, I was able to not only create a common location for CSS files, but to take it a step farther and use Less in ordr to simplify that common CSS even further.
url: /index.php/webdev/serverprogramming/aspnet/using-t4-templates-for-centralized/
views:
  - 8324
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Javascript
tags:
  - javascript
  - t4-template

---
In my previous post I mentioned that I was looking for an answer to the age-old question of how to manage common CSS and JavaScript across multiple projects (specifically ASP.Net projects). Using T4 templates, I was able to not only create a common location for CSS files, but to take it a step farther and use Less in ordr to simplify that common CSS even further.

But we left JavaScript out of the equation.

## T4&#8217;ing the JavaScript

As a reminder, I am looking for a clean way to store shared JavaScript and CSS in a single place so I share it amongst several web projects and also make changes to the files on the fly and see them in the browser without a costly solution rebuild.

On the JavaScript side of things I decided to write my own little T4 template. The goal was to be able to pick up all of the *.js files in a central location, merge them, and save them out as a common file that I could then include in my local projects. 

My solution layout is similar to the one in the prior post:

```text
/SolutionName/Common/NN-*.js files
/SolutionName/Project1/scripts/CommonJS.tt
/SolutionName/Project2/scripts/CommonJS.tt
```
I prefix my common javascript files with a 2-digit number so I can include them in a planned order every time.

**CommonJS.tt**

```C#
<#@ template language="C#" hostspecific="True" #>
<#@ import namespace="System" #>
<#@ import namespace="System.IO" #>
<#@ import namespace="Microsoft.VisualStudio.TextTemplating" #>
<#@ Output Extension=".js" #>
<#
/*-------------------------------------------------*/
// Settings      
/*-------------------------------------------------*/
string _targetDirectory = @"....Common";
/*-------------------------------------------------*/
Directory.SetCurrentDirectory(Path.GetDirectoryName(Host.TemplateFile) + _targetDirectory);

var filespec = "*.js";
var files = Directory.GetFiles(".",filespec);
#>
/*
CommandJavascript.js
Converted at: <#= DateTime.Now.ToShortDateString() #> <#= DateTime.Now.ToShortTimeString() #>
File List (<#= files.Length #> Found):
<#= "t" + String.Join("nt", files) #>
*/
<#
foreach(var jsFile in files) 
{
	Write("/* ----------- " + jsFile + " ----------- */n");
	using(StreamReader sr = File.OpenText(jsFile))
	{
		Write(sr.ReadToEnd());
		sr.Close();
	}
	Write("n/* ----------- " + jsFile + " ----------- */nn");
}
#>
```
Place this file in our project, update the _targetDirectory variable and we have a quick way to pull common javascript down to our projects.

The output file is a merged version of all of the *.js files found in the common folder. I also output comments around each file block to make it easy to track back to the original file, if needed.

## The Price of Hack and Slash Code

I put this script together very quickly and, as such, it is missing some features I think would be nice additions. The ability to execute a minification run at the end would be nice, as would the [MarkDirty][1] feature that was in the T4CSS.tt code. 

As with the previous post, the [sample project is available][2], so you can look at (or download) a working example if you would like.

And that is that. Shortest Eli post ever? Maybe 🙂

 [1]: http://blogs.msdn.com/davidebb/archive/2009/06/26/the-mvc-t4-template-is-now-up-on-codeplex-and-it-does-change-your-code-a-bit.aspx "MVC T4 article that discusses the concept of saving a template file to force regeneration"
 [2]: https://bitbucket.org/tarwn/aspnet_sharedresourceswitht4/overview "Sample project on BitBucket"