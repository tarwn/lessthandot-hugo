---
title: Working with Git my first impressions
author: Christiaan Baes (chrissie1)
type: post
date: 2010-09-10T05:19:59+00:00
ID: 895
excerpt: |
  Introduction
  
  A couple of weeks ago I introduced myself to the world of the world of Git and Github. And I did not see much wrong with it. But over the last couple of years I have become very frustrated with the slowness of SVN - or it could just be T&hellip;
url: /index.php/desktopdev/mstech/working-with-git-my-first-impressions/
views:
  - 3185
categories:
  - General Purpose Languages
  - Microsoft Technologies
tags:
  - git
  - msysgit
  - tortoisegit

---
## Introduction

A couple of weeks ago I introduced myself to the world of [the world of Git and Github][1]. And I did not see much wrong with it. But over the last couple of years I have become very frustrated with the slowness of SVN &#8211; or it could just be TortoiseSVN. Anyway, I thought it was time for a change. I am still a lonely developer in a non-IT oriented workplace, so I mainly use source control for Backup as well as for easy Continuous Integration. Every time I commit somewhere, a server starts to build the project and run the unit tests like magic. But now it is time to move on and lose some time with learning a new thing again. I guess using Github or some other third party external thing would be cool but it is not an option in my line of business.

## First attempt

My first attempt was to create a Ubuntu machine and set it up as a central repository. I got the Ubuntu machine installed and Git working on it but I could not get the client to communicate with the server because of a firewall issue so I gave up. Mind you, that was after a fight of 5 hours with downloads, Kubuntu , proxy servers, apt-get, VMWare, more proxy madness,&#8230;

## I saw the light

I saw the light. During all of the various installations I had a lot of time to surf the world wide web and saw that I did not need a server. I could just use a share and set that up as a central repository. This is fairly easy once you understand how it works.

### Prerequisites

You will need to have [msysgit][2] and [toroisegit][3] installed on this system &#8211; just look at my [previous post][1] on how to install both. No need for Putty since there is no ssh involved.

### Central repository

So first we create a share on a server somewhere. Then we create a folder on that share; I guess you all know how to do that. If you don&#8217;t, then step away from the keyboard.
  
Now you have to open a command window and go to the folder you just created. Or just use explorer shift+right-click on the folder and choose &#8220;Open command window here&#8221;. And now for the hard part. Type 

```
git --bare init```
[According to this.][4] 

> A short aside about what git means by bare: A default git repository assumes that you will be using it as your working directory, so git stores the actual bare repository files in a .git directory alongside all the project files. Remote repositories don&#8217;t need copies of the files on the file system unlike working copies, all they need are the deltas and binary what-nots of the repository itself. This is what &#8220;bare&#8221; means to git. Just the repository itself. 

And this is what it looks like after you do the command-line thingie.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/git/git1.png" alt="" title="" width="845" height="354" />
</div>

### Local repository

Now that we have our central repository set up we need to set up our local repository. For this we go to My Documents and create a folder in there. Right click on the folder and click Git Clone.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/git/git2.png" alt="" title="" width="248" height="64" />
</div>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/git/git7.png" alt="" title="" width="485" height="364" />
</div>

The url to fill in is the folder where you have setup your central repository.

Now we just create a few files and folders in the local repository and commit them to master (master being the branch, but more about that in the next post).

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/git/git4.png" alt="" title="" width="245" height="69" />
</div>

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/git/git3.png" alt="" title="" width="845" height="354" />
</div>

This commit (unlike the SVN commit) just commits things locally. To make the central repository receive our changes we need to do a push.

Via this menu.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/git/git5.png" alt="" title="" width="432" height="149" />
</div>

Or via the Git sync&#8230; menu item.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/git/git6.png" alt="" title="" width="580" height="465" />
</div>

Normally the remote should have been setup by your cloning action and should be correct. 

Now if we go back to our central repository it will just look the same as before.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/git/git1.png" alt="" title="" width="845" height="354" />
</div>

But the files are there. We can see this best by creating a second local repository and doing the clone thing again. And here is the result.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/git/git8.png" alt="" title="" width="845" height="354" />
</div>

This is magic stuff and very easy to do.

## Conclusion

Using Git with Tortoisegit is easy, using Git and making a central repository on a share is easy. What more do you need? My next attempt at a blog will be about branching and merging which is easy in Git. And in the future I also want to try out Git-extensions because they have a VS plug-in.

 [1]: /index.php/All/?p=931
 [2]: http://code.google.com/p/msysgit/downloads/list
 [3]: http://code.google.com/p/tortoisegit/
 [4]: http://www.jedi.be/blog/2009/05/06/8-ways-to-share-your-git-repository/#fileshare