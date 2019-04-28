---
title: The roslyn syntax visualizer tool window
author: Christiaan Baes (chrissie1)
type: post
date: 2011-10-23T12:07:00+00:00
ID: 1358
excerpt: |
  When I installed Roslyn I thought the visualizer tool window would just be there; But it isn't. You need to install it. And you need to install it in an awkward way ;-).
  
  First you need to open the Getting started document.
  
  
  
  Then you should read&hellip;
url: /index.php/desktopdev/mstech/the-roslyn-syntax-visualizer-tool/
views:
  - 7671
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - roslyn ctp
  - vb.net

---
When I installed Roslyn I thought the visualizer tool window would just be there; But it isn&#8217;t. You need to install it. And you need to install it in an awkward way ;-).

First you need to open the Getting started document.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn10.png?mtime=1319377387"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn10.png?mtime=1319377387" width="275" height="81" /></a>
</div>

Then you should read the whole document, of course. If you did that you can click on the link that says Syntax Visualizer Tool Window

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn11.png?mtime=1319377523"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn11.png?mtime=1319377523" width="1070" height="663" /></a>
</div>

You can find that solution file in your my documents under Microsoft Codename Roslyn CTP &#8211; October 2011SharedSyntaxVisualizerExtension

This will open a solution for you. That solution also contains an extension project.
  
And that project contains this in a readme file.

> The Roslyn Syntax Visualizer is a Visual Studio Extension that will enable inspection of &#8216;live&#8217; Roslyn SyntaxTrees for any C# or VB code file that is open inside the Visual Studio IDE when the Roslyn Language Service is present (e.g. when running or debugging other Roslyn extensions). 

You can now run this solution. And open our previous created Roslyn Console Application. 

If you now go to the View->Other windows you will find a Roslyn syntax visualizer menu item. 

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn13.png?mtime=1319378501"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn13.png?mtime=1319378501" width="615" height="478" /></a>
</div>

If you open this and open the class1 file you will see this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn12.png?mtime=1319378584"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn12.png?mtime=1319378584" width="915" height="440" /></a>
</div>

And if you want to see that they need to do more work on Roslyn than you should open the Modult1 file ;-).

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/roslyn/roslyn14.png?mtime=1319378679"><img alt="" src="/wp-content/uploads/users/chrissie1/roslyn/roslyn14.png?mtime=1319378679" width="1155" height="384" /></a>
</div>