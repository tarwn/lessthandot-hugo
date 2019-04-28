---
title: Nuget makes life so much easier
author: Christiaan Baes (chrissie1)
type: post
date: 2011-05-01T10:14:00+00:00
ID: 1151
excerpt: |
  Introduction
  
  Nuget has been around for a while now, but what a program like this needs is content, content and more content. And Nuget now has plenty of that. SO if you use opensource libraries alot and you are not yet using Nuget then do it now.&hellip;
url: /index.php/desktopdev/mstech/nuget-makes-live-so-much/
views:
  - 1591
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - nuget

---
## Introduction

[Nuget][1] has been around for a while now, but what a program like this needs is content, content and more content. And Nuget now has plenty of that. SO if you use opensource libraries alot and you are not yet using Nuget then do it now.

## How it helps

Now when I set up a testproject I use nuget. All my favorite packages are there and it takes seconds to install them. But I can also create my own package that has a dependency on all the packages I want and install everything in one go.

Typically my testprojects would have references to Nunit, Rhino mocks and Structuremap.automocking. To have all this in one package I downloaded [Nuget Package explorer][2].

I then select File -> New and then Edit -> Edit package Metadata.

You can then fill in all the fields you want.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nuget/nuget5.png?mtime=1304251123"><img alt="" src="/wp-content/uploads/users/chrissie1/nuget/nuget5.png?mtime=1304251123" width="1000" height="765" /></a>
</div>

As you can see I already picked the dependecies I want from the feed.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nuget/nuget10.png?mtime=1304251176"><img alt="" src="/wp-content/uploads/users/chrissie1/nuget/nuget10.png?mtime=1304251176" width="640" height="481" /></a>
</div>

I can then close that edit menu and my package now looks like this.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nuget/nuget6.png?mtime=1304251134"><img alt="" src="/wp-content/uploads/users/chrissie1/nuget/nuget6.png?mtime=1304251134" width="1000" height="765" /></a>
</div>

You now have to save it via File -> Save

Now I can go to Visual studio where I already have nuget installed. Just open Tools -> Library Package Manager -> Package Manger Settings. Where you will see this under Package Sources.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nuget/nuget7.png?mtime=1304251144"><img alt="" src="/wp-content/uploads/users/chrissie1/nuget/nuget7.png?mtime=1304251144" width="787" height="471" /></a>
</div>

Now browse (the three dots) for the folder where you left you little package and click Add.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nuget/nuget8.png?mtime=1304251166"><img alt="" src="/wp-content/uploads/users/chrissie1/nuget/nuget8.png?mtime=1304251166" width="787" height="471" /></a>
</div>

Now you can rightclick your project and do Add Library Package Reference. Search for you package and click install.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nuget/nuget4.png?mtime=1304251113"><img alt="" src="/wp-content/uploads/users/chrissie1/nuget/nuget4.png?mtime=1304251113" width="830" height="673" /></a>
</div>

<span class="MT_red">Be carefull You have to select the All source or else this will not work because of the external references.</span>

And after all that you have all the references you need in your product.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/nuget/nuget9.png?mtime=1304251157"><img alt="" src="/wp-content/uploads/users/chrissie1/nuget/nuget9.png?mtime=1304251157" width="443" height="344" /></a>
</div>

## Conclusion

Once you have your nuget package set up, which only takes a few minutes, setting up your project with the necessary references will take seconds. So you will save yourself hours of looking on the web in the future. You can now spend those hours browsing Lessthandot.

 [1]: http://nuget.codeplex.com/
 [2]: http://nuget.codeplex.com/releases/59864/clickOnce/NuGetPackageExplorer.application