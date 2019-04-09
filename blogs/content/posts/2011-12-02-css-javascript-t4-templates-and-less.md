---
title: CSS, Javascript, T4 Templates, and Less, Oh My
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-12-02T13:02:00+00:00
ID: 1401
excerpt: For the past few months, I have been looking for a way to define some JS and CSS files that would be shared between multiple projects in an ASP.Net solution. The intent is to define common scripts and CSS in one place instead of trying to keep multiple copies of it in sync or implementing an internal CDN with a versioning scheme. The challenge is finding a way to do this with a minimum of impact on the development, deployment, and production processes.
url: /index.php/webdev/serverprogramming/aspnet/css-javascript-t4-templates-and-less/
views:
  - 6179
rp4wp_auto_linked:
  - 1
categories:
  - ASP.NET
  - Javascript
  - XHTML and CSS
tags:
  - css
  - javascript
  - less
  - t4-template

---
For the past few months, I have been looking for a way to define some JS and CSS files that would be shared between multiple projects in an ASP.Net solution. The intent is to define common scripts and CSS in one place instead of trying to keep multiple copies of it in sync or implementing an internal CDN with a versioning scheme. The challenge is finding a way to do this with a minimum of impact on the development, deployment, and production processes.

## The Shared Project

Yesterday the best solution I was also the best one I had thought of on my own, which was to create a shared project and use pre- or post-build commands to copy the common files to the relevant web projects. Unfortunately this doesn't solve the “let me edit a file without rebuilding” unless I then edit the copied file, test, and remember to paste the changes back into the source without first building and wiping out my temporary changes.

Yuck.

This wasn't going to make life that much easier.

## Start with just the CSS?

This morning, while looking into the potential of using LESS or SASS to reduce the repetitiveness of the files, I realized I had the potential for a much better solution. If I had a way to compile a LESS or SASS file on the fly into a CSS file, then I could still put my common CSS in a central location and just use an import statement in a template in each project to pull those common values in. 

I initially looked at using SASS with the [Mindscape Web Workbench][1] plugin. This seemed like a good solution, but something I read recently about design-time T4 templates led me to wonder if someone had created a T4 template that would transform LESS or SASS syntax into a nice clean CSS file. 

What did we do before search engines…

## Implement T4CSS Template

Phil Haack ([blog][2]|[twitter][3]) posted a blog in 2009 on [exactly this topic][4]. He created a T4 template for Visual Studio 2008 that would use the dotless C# assembly to convert LESS files to static CSS files and provided to the [.Less site][5].

Now we're cooking.

First we need to download the t4css package from github: https://github.com/dotless/dotless/downloads

There are two files we are concerned with, the dotless.Core DLL and the T4CSS.tt template file. The template file is placed in our CSS folder in our site. 

<div style="font-size: .9em; background-color: #eeeeee; padding: .5em;">
<h3>
Referencing the Assembly
</h3>

<p>
Unfortunately Visual Studio 2010's T4 implementation no longer accesses assemblies through the project references, but this still leaves us with <a href="http://weblogs.asp.net/lhunt/archive/2010/05/04/t4-template-error-assembly-directive-cannot-locate-referenced-assembly-in-visual-studio-2010-project.aspx" title="T4 Template error - Assembly Directive cannot locate referenced assembly in Visual Studio 2010 project">a few options</a>. Given that I want to share this among several projects, I put the dotless.Core DLL in a folder at my solution level and updated the path in the T4 template to use the solution path macro. </div> 

<p>
  Next I created a sample file to play with, which I called test.less.css (fancy, I know). I also modified the settings section of the T4CSS.tt file, setting _runOnBuild and _useCssExtension to “true”. This will cause the template to run on each build, as well as when I trigger it, and it will look for files ending in “.less.css” instead of just “.less”. This gives us some CSS intellisense with minimal hassle, though Mindscape's Web Workbench apparently handles this out of the box and there is <a href="http://visualstudiogallery.msdn.microsoft.com/dd5635b0-3c70-484f-abcb-cbdcabaa9923" title="CSS Is Less">an extension</a> to make VS treat the less extension as a CSS format.
</p>

<div style="font-size: .9em; background-color: #eeeeee; padding: .5em;">
  There is also a <a href="http://visualstudiogallery.msdn.microsoft.com/e646c6ec-87a7-45ea-81e8-d655a3d3e73e?SRC=VSIDE" title="LessExtension">LessExtension</a> in the gallery that seems to offer some of the functionality I already have with the T4 template, but I didn't have a chance to play with it.
</div>

<p>
  If we keep the T4CSS file open, it will mark itself as unsaved each time it runs, so using Ctrl+Shift+S will save it and regenerate the output CSS. At least that's the theory. Unfortunately in my case, it seems that the template file was being saved prior to the css file, so I've taken to pressing Ctrl+S and then Ctrl+Shift+S after making a quick change in my CSS (which is still way better than a Rebuild All would be).
</p>

<p>
  <i>Note: There is also the “Transform All Templates” button on the top of the solution explorer if I don't feel like double-saving. I could also add a shortcut in the keyboard commands list (Tools -> Options -> Environment -> Keyboard) for “TextTransformation.TransformAllTemplates</i>
</p>

<h2>
  Working Across Projects
</h2>

<p>
  This solution hasn't quite given me the “save the file and refresh the page” ease of use of a static CSS file. This means if you are editing a less file that more than one template references, and you don't have all the templates open, you could get out of sync. To help keep things clean in source control, this means you should run a complete build (to let all the transforms run) or use.
</p>

<p>
  To make this work for multiple projects we can add a folder at the solution level with our less files and use the @import statement to pull them in. Except now we can't do the .less.css trick anymore because less doesn't process @import's ending in CSS, assuming they are intended to be regular css imports. At this point, it's probably time to stop fighting it, apply the “Less is CSS” extension I mentioned above, and change back to using .less instead of .less.css. Fun times.
</p>

<p>
  So what we end up with is a folder structure that looks like:
</p>

<pre lang="text">	
/SolutionName/Common/dotless.Core.dll
/SolutionName/Common/common.less
/SolutionName/Project1/css/T4CSS.tt
/SolutionName/Project1/css/stylesheet.less
/SolutionName/Project2/css/T4CSS.tt
/SolutionName/Project2/css/stylesheet.less
</pre>

<p>
  And inside the project-specific .less files we have an import at the top, like so:
</p>

<pre lang="css">
@import "../../Common/common.less";

/* plus some project specific-stuff */
</pre>

<p>
  And there we have it, shared CSS. I posted a <a href="https://bitbucket.org/tarwn/aspnet_sharedresourceswitht4/overview" title="See project on BitBucket">working sample project on BitBucket</a> if you want to browse it in detail.
</p>

<h2>
  I Specifically Heard You Say JavaScript
</h2>

<p>
  I intend to solve the common javascript issue the same way, except in this case I will write my own T4 templates to directly copy the files from the common area that are needed in each project. This will provide me with an easy way to manage common scripts in a central location, the ability to edit and refresh my page to test changes without rebuilds, and can easily be extended to include minified versions of the files.
</p>

 [1]: http://visualstudiogallery.msdn.microsoft.com/2b96d16a-c986-4501-8f97-8008f9db141a "Mindscape Web Workbench extension on VisualStudioGallery"
 [2]: http://haacked.com/ "Phil's blog"
 [3]: https://twitter.com/#!/haacked "@haacked on twitter"
 [4]: http://haacked.com/archive/2009/12/02/t4-template-for-less-css.aspx "T4CSS: A T4 Template for .Less CSS With Compression"
 [5]: http://www.dotlesscss.org/ "Visit the .Less Site"