---
title: How I work with git
author: Christiaan Baes (chrissie1)
type: post
date: 2010-09-23T06:44:39+00:00
ID: 904
url: /index.php/desktopdev/mstech/vbnet/how-i-work-with-git/
views:
  - 10496
categories:
  - General Purpose Languages
  - VB.NET
tags:
  - git
  - vb.net

---
## Introduction

A little while ago I [switched from SVN to Git][1]. I also explained [how to install TortoiseGit][2]. Now here are my conclusions after working with it for a bit.

## To GUI or not to GUI

I&#8217;m a GUI man myself, if I can click it than I will. Typing seems a waste of time if the solution is just one click away. So my first instinct was to install a GUI, in this case that was TortoiseGit, which seemed the logical choice since I had used TortoiseSVN before.

But I don&#8217;t think the Git-workflow benefits much from a GUI. Perhaps the GUI is convenient for some of the lesser known commands but it sure holds you back for the more wildly used commands. Now I just have a command prompt window open all the time.

These are the commands I would use on a regular basis.

<code class="codespan">git commit -a -m "Type your message here"</code>

With git a commit is to your local repository and the current branch (branching is very important with git). The message is not really optional. And the commit often mantra is a breeze with git since it commits so fast. So your messages should be short. That way you can use them to get some reports out of them, like showing the Boss what you did and when you did it.

<code class="codespan">git add -A</code>

This command actually goes before the commit command. A git commit -a will only commit the files that changed and the files that were deleted, it will not pickup the new files. For this you need the add command and the -A just makes sure you catch them all instead of having to remember which ones you added during your fight with the code.

<code class="codespan">git push</code>

This command pushes your local repository to the central repository it is linked to. This also is on your current branch.

<code class="codespan">git branch</code>

This command will show you what branches you have and which one you are working on. Did I mention that branching is easy in Git? Unlike SVN where each branch is a different folder in git some magic happens. You just use the folder you normally use and files and folders within it just magically appear and disappear depending on the branch. Really cool stuff, and fast too.

<code class="codespan">git branch &lt;branchname&gt;</code>

This is a command you should use very often. Everytime I add a new feature (big or small) I will create a new branch, just because it is so easy and fast. This will create a branch from your current branch.

<code class="codespan">git checkout &lt;branchname&gt;</code>

This command will switch to the branch you want. So that you and VS will start working with it. But I have noticed that VS is not always all that happy when you change branches mid-flight. Especially the .solution and project files do not always pickup the changes when you change to another branch while it is open.

<code class="codespan">git merge &lt;branchname&gt;</code>

This will merge your changes of <branchname> to your current branch. As long as you have no problems with the merge I would use this. But as soon as you do have a more difficult merge to do I would switch to a GUI instead.

The cloning and setting up of the repos I would still do via TortoiseSVN.

## Conclusion

Use Git.

<span class="MT_smaller"><br /> Only 7 posts untill my 250th and final post.</span></branchname>

 [1]: /index.php/All/?p=956
 [2]: /index.php/All/?p=931