---
title: Powershell and tabexpansion
author: Christiaan Baes (chrissie1)
type: post
date: 2012-02-15T10:21:00+00:00
ID: 1535
excerpt: I find the whole tabexpansion thing in powershell intriguing and I was especially interested in what posh-git did. Since I wanted to use that in my own scripts too.
url: /index.php/desktopdev/mstech/powershell-and-tabexpansion/
views:
  - 2772
categories:
  - Microsoft Technologies
tags:
  - powershell
  - tabexpansion

---
I find the whole tabexpansion thing in powershell intriguing and I was especially interested in [what posh-git did][1]. Since I wanted to use that in my own scripts too.

To be clear tabexpansion is when you type something and then do tab and the IDE tries to figure out what comes next.

Like that.

<div class="image_block">
  <a href="/wp-content/uploads/users/chrissie1/poshgit/poshgit2.png?mtime=1328164953"><img alt="" src="/wp-content/uploads/users/chrissie1/poshgit/poshgit2.png?mtime=1328164953" width="739" height="135" /></a>
</div>

That&#8217;s cool, since us old people are not good at remembering things so we need these kinds of things to help us. 

Now how do you add that to your custom function. In essence it is really simple. You just override the default one like this. 

```
function TabExpansion([string] $line, [string] $lastword) {
   write-host "chris was here"
   return null
}```
Just try the above and see what it does. 

Yeah, annoying. 

What is important in the above is that it returns null. This means that powershell now knows it has to pass control back to the overriden function, which is then going to go ahead with what it normally does.

If however I do this.

```
function TabExpansion([string] $line, [string] $lastword) {
   return "chris was here"
}```
The only thing you will ever see now is chris was here whenever you try tabcompletion. How much fun is that ;-).

You can also return an array of strings and then everytime you hit tab it will loop through the elements you passed in.

```
function TabExpansion([string] $line, [string] $lastword) {
   return "chris was here", "and here", "and there"
}```
Even more fun.

But I&#8217;m sure that is not what you want.

Now if we go look in the GitTabExpansion.ps1 file from posh-git you will see this.

```
if (Test-Path Function:TabExpansion) {
    Rename-Item Function:TabExpansion TabExpansionBackup
}

function TabExpansion($line, $lastWord) {
    $lastBlock = [regex]::Split($line, '[|;]')[-1].TrimStart()

    switch -regex ($lastBlock) {
        # Execute git tab completion for all git-related commands
        "^$(Get-AliasPattern git) (.*)" { GitTabExpansion $lastBlock }
        "^$(Get-AliasPattern tgit) (.*)" { GitTabExpansion $lastBlock }

        # Fall back on existing tab expansion
        default { if (Test-Path Function:TabExpansionBackup) { TabExpansionBackup $line $lastWord } }
    }
}```
Now what is important in the above is that posh-git overwrites the tabexpansion function but first renames the one that is already there. 

And why does it do that you ask?

Well, you have to be aware that you are not the only person that wants to overwrite tabexpansion there might be other people wanting to the same and we don&#8217;t always want to loose what those people did. So we make a copy (or rename) their function first. <span class="MT_red">Be aware someone might have had the same brilliant idea as posh-git and might have also named their copy TabExpansionBackup and that&#8217;s when you are in trouble.</span>

And that is all I needed to know about TabExpansion in powershell.

 [1]: /index.php/DesktopDev/MSTech/posh-git-in-the-nuget