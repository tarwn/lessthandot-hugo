---
title: Moving a git repository hosted on a share
author: Christiaan Baes (chrissie1)
type: post
date: 2011-09-15T05:29:00+00:00
ID: 1316
excerpt: |
  A while ago I made myself a git repository on a share. It's a fairly easy process and works very well and fast. But now I wanted to move that repo to a new place and searched the webs on how to do this. 
  
  I found nothing that was just copy-pasteable b&hellip;
url: /index.php/enterprisedev/application-lifecycle-management/moving-a-git-repository-hosted/
views:
  - 1708
categories:
  - Application Lifecycle Management
tags:
  - git

---
a git repository on a share. It&#8217;s a fairly easy process and works very well and fast. But now I wanted to move that repo to a new place and searched the webs on how to do this. 

I found nothing that was just copy-pasteable but I got it working and it is very easy.

You can find lots of other ways to move a git repo, but they all seemed so complex.

  * [Moving your git repos and all branches to a new remote repository][1]
  * [Moving files from one git repository to another preserving history][2]
  * [How do you move your Git repository from one server to another?][3]

This is therefor going to be a very short blogpost.

You just copy paste the folder containing the bare repo over to the new place.

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/git/git.png?mtime=1316071284"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/git/git.png?mtime=1316071284" width="587" height="191" /></a>
</div>

A while ago I made myself </p> 

And then you change the origin of your current repo. 

> git config remote.origin.url newlocation

[A trick that I found on StackOverflow][4].

And that&#8217;s it. Simple as pie.</a>

 [1]: http://blog.dynamic50.com/2009/11/26/moving-your-git-repos-and-all-branches-to-a-new-remote-repository/
 [2]: http://gbayer.com/development/moving-files-from-one-git-repository-to-another-preserving-history/
 [3]: http://serverfault.com/questions/135573/how-do-you-move-your-git-repository-from-one-server-to-another
 [4]: http://stackoverflow.com/questions/3011402/leaving-github-how-to-change-the-origin-of-a-git-repo/5635412#5635412