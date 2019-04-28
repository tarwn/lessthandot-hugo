---
title: Git checkout and Visual studio a solution with powerconsole
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-27T06:39:00+00:00
ID: 1060
excerpt: 'I started my journey on Thursday when I tried to automate the task of closing Visual Studio doing the git checkout and then starting Visual studio again. I failed on that account, and I found that my win7 installation was a bit corrupt,  but I got some&hellip;'
url: /index.php/desktopdev/generalpurposelanguages/git-checkout-and-visual-studio/
views:
  - 2304
categories:
  - General Purpose Languages
tags:
  - powershell
  - visual studio 2010

---
I started my journey on Thursday when [I tried to automate the task of closing Visual Studio][1] doing the git checkout and then starting Visual studio again. I failed on that account, and [I found that my win7 installation was a bit corrupt][2], but I got some good help and [a comment][3] that got me thinking. So I continued on my quest and on Saturday [I installed powerconsole][4] and [found what I could do with it][5]. By the end of the day and many blogposts later I found a solution that worked. Here is that solution.

```
Function checkout($branch)
{
  solutionworkingdirectory
  $filename = $dte.Solution.FileName
  $dte.Solution.Close()
  git add .
  git commit -a -m "auto commit before checkout"
  git checkout $branch
  $dte.Solution.Open($filename)
}```
Looks very simple and elegant indeed.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole11.png?mtime=1298795805"><img alt="" src="/wp-content/uploads/users/chrissie1/powerconsole/powerconsole11.png?mtime=1298795805" width="718" height="769" /></a>
</div>

The only problem being that even a successful checkout seems to result in a exception. But it does not bother me to much. I can now do everything from within Visual Studio and I saved a few keystrokes at the same time.

 [1]: /index.php/DesktopDev/GeneralPurposeLanguages/my-first-steps-in-powershell
 [2]: http://
 [3]: /index.php/DesktopDev/GeneralPurposeLanguages/my-first-steps-in-powershell#c7779
 [4]: /index.php/DesktopDev/GeneralPurposeLanguages/power-console-for-visual-studio
 [5]: /index.php/DesktopDev/GeneralPurposeLanguages/powerconsole-and-removing-the-git