---
title: powerconsole and removing the git from my git routine
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-26T08:40:00+00:00
ID: 1057
excerpt: |
  Introduction
  
  You know you have been using git to long when you start typing git before every command in the command prompt. Typing git before every command is also 4 characters to many. I don't even see why I should do that in the Git bash, doesn't i&hellip;
url: /index.php/desktopdev/generalpurposelanguages/powerconsole-and-removing-the-git/
views:
  - 3098
categories:
  - General Purpose Languages
tags:
  - git
  - powerconsole

---
## Introduction

You know you have been using git too long when you start typing git before every command in the command prompt. Typing git before every command is also 4 characters to many. I don&#8217;t even see why I should do that in the Git bash, doesn&#8217;t it know why I&#8217;m there. It&#8217;s boring and I have better things to do with my time. SO it&#8217;s time for a solution.

## Powerconsole

I told you [how to install powerconsole in a previous post][1]. But you can also make your own function to use in powerconsole. 

Here is where you can find the Powerconsoleuserprofile or at least that is where you are supposed to find it.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powerconsole/powerconsole7.png?mtime=1298715741"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powerconsole/powerconsole7.png?mtime=1298715741" width="532" height="70" /></a>
</div>

If you try the notepad command and get this.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powerconsole/powerconsole8.png?mtime=1298715830"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powerconsole/powerconsole8.png?mtime=1298715830" width="861" height="307" /></a>
</div>

you better get your explorer open and create the file.

after that is done we can add these commands to it.

```
Function solutionworkingdirectory()
{
  split-path -parent $dte.Solution.FileName | cd
}

Function branch()
{
  solutionworkingdirectory
  git branch
}```
after a restart of Visual Studio. I can now do this. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powerconsole/powerconsole9.png?mtime=1298716073"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powerconsole/powerconsole9.png?mtime=1298716073" width="298" height="91" /></a>
</div>

> <span class="MT_red">Big warning.<br /> If you have any syntax error anywhere in that ps1 file than the console will just ignore all functions in that file. Not just one ALL of them. I know I tried.</span>

After that I added 2 more functions to it.

```
Function branch($branch)
{
  solutionworkingdirectory
  git branch $branch
}

Function commit($message)
{
  solutionworkingdirectory
  git add .
  git commit -a -m "$message"
}```
With this as the result.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powerconsole/powerconsole10.png?mtime=1298716516"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/powerconsole/powerconsole10.png?mtime=1298716516" width="863" height="496" /></a>
</div>

I have just save myself a gazilion number of keystrokes. I hope I saved you some too.

## Conclusion

Powerconsole can make you life easier. Now I just need to automate the checkout routine.

 [1]: /index.php/DesktopDev/GeneralPurposeLanguages/power-console-for-visual-studio