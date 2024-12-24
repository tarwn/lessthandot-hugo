---
title: Git trouble because I screwed up something
author: Christiaan Baes (chrissie1)
type: post
date: 2011-03-16T11:55:00+00:00
ID: 1079
excerpt: 'I got in a bit of git trouble today. I had a branch where I had been working on for a while. For some reason when I wanted to merge this branch back into master it did not add the changes I had made in that branch. At this time of distress Google is alw&hellip;'
url: /index.php/desktopdev/mstech/vbnet/git-trouble/
views:
  - 1547
categories:
  - 'C#'
  - VB.NET
tags:
  - git
  - reflog
  - reset

---
I got in a bit of git trouble today. I had a branch where I had been working on for a while. For some reason when I wanted to merge this branch back into master it did not add the changes I had made in that branch. At this time of distress Google is always at hand to help and sometimes cause more problem.

In the time I was trying to resolve my little predicament I learned a few new commands.

First on the list I tried was 

<code class="codespan">git reset --hard HEAD~1</code>

this resets the current branch to master -1 commit. you can change the number to whatever you want to go back as many commits if you want.

I also used this

<code class="codespan">git reset --hard branchname</code>

that one resets your current branch to the branch you named.

and then I learned

<code class="codespan">git reflog</code>

which will give you a nice list of what you did. 

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/git/ScreenShot035.png?mtime=1300283503"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/git/ScreenShot035.png?mtime=1300283503" width="677" height="392" /></a>
</div>

And yes I made a mess of things.

And finaly you can do.

<code class="codespan">git reset --hard a8bb565</code>

which will let you back to any of the commits by Id, you can see the id&#8217;s of each commit in the reflog above.

So screwing up is educational sometimes.