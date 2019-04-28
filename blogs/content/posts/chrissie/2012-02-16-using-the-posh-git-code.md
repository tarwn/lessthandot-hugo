---
title: Using the posh-git code in my own scripts to make my git experience with Visual studio even better.
author: Christiaan Baes (chrissie1)
type: post
date: 2012-02-16T07:03:00+00:00
ID: 1539
excerpt: "Using the posh-git code in my own scripts to make my git experience with Visual studio even better. Yep, that's what the title already said."
url: /index.php/desktopdev/mstech/using-the-posh-git-code/
views:
  - 1870
categories:
  - Microsoft Technologies
tags:
  - git
  - posh-git
  - powershell
  - visual studio

---
Nice long title says it all.

So you know [I use posh-git and I use it in the nuget package manager console][1] or the [studioshell plugin][2]. 

ANd now I want to join what I had in those previous post and throw in some [tabexpansion][3] to make my checkout script better.

I want it to show my branches when I type <code class="codespan">checkout &lt;tab&gt;</code> in the console. 

So in my $profile I have this script.

```powershell
Function solutionworkingdirectory()
{
  split-path -parent $dte.Solution.FileName | cd
}

Function checkout($branch)
{
  solutionworkingdirectory
  write-host "Closing solution"
  $filename = $dte.Solution.FileName
  $dte.Solution.Close()
  write-host "Adding files"
  git add .
  write-host "Commiting files"
  git commit -a -m "auto commit before checkout"
  write-host "Checking out"
  git checkout $branch
  write-host "Reopening solution"
  $dte.Solution.Open($filename)
}```
That script closes the solution, adds all files, does a commit, checksout the code to the branch I want and then reopens the solutions. 

And why do we do this? Because Visual studio freaks out if you don&#8217;t. Very weird things can happen when you leave the solution open and the branches are not inn sync. Especially bad things happen when the solution has file open that the current branch doesn&#8217;t have and is not supposed to have. It is also annoying in that it will try to reload the projects one by one. But there is a plugin for that. Anyway it&#8217;s not good and the above script deals with that. 

Now the above does not have tabCompletion because powershell has no idea what I want. But with the information we have in our tabExpansion post and [the previous post][1] I made. We can now do that.

So for this script to work you need to have posh-git installed.

```powershell
. c:posh-gitGitTabExpansion.ps1

if (Test-Path Function:TabExpansion) {
    Rename-Item Function:TabExpansion TabExpansionBackupForMyNugetProfile
}

function TabExpansion([string] $line, [string] $lastword) {
   if($line -eq "checkout ")
   {
     $branch  = gitBranches $lastword $false
     return $branch
   }
   else
   {
     TabExpansionBackupForMyNugetProfile $line $lastword
   }
   
}```
as you can see I dot-source the GitTabExpansion scriptfile so that I can use the gitBranches function that posh-git provides for me. 

The parameters to give are a string with the first letters you have already typed and the <code class="codespan">$includeHEAD</code> parameter that leaves or removes the head branches from the list. If you type nothing then you get the complete list.

 [1]: /index.php/desktopdev/mstech/posh-git-in-the-nuget/
 [2]: /index.php/desktopdev/mstech/studioshell-more-powershell-for-visual/
 [3]: https://www.google.com/url?q=/index.php/desktopdev/mstech/powershell-and-tabexpansion/