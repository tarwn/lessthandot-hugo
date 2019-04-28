---
title: Git, TortoiseGit, Github and the rest
author: Christiaan Baes (chrissie1)
type: post
date: 2010-08-15T16:48:47+00:00
ID: 871
url: /index.php/desktopdev/mstech/git-tortoisegit-github-and-the-rest/
views:
  - 17005
categories:
  - Microsoft Technologies
tags:
  - git
  - githubtortoisegit
  - nsubstitute
  - structuremap

---
## Introduction

This weekend I felt the need to make myself an [NSubstitute][1] Automocker for [StructureMap][2]. The first thing to do was to download the sourcecode for [StructureMap][2] and [NSubstitute][1]. Both are being hosted on [GitHub][3]. because I wanted to fork StructureMap and add the Automocker to it I had to create an account on github, which is free. And then I had to install Git and download the code.

For those people that don&#8217;t know what Github is.
  
[According to wikipedia][4].

> GitHub is a web-based hosting service for projects that use the Git revision control system. It is written using Ruby on Rails by GitHub, Inc. (previously known as Logical Awesome) developers Chris Wanstrath, PJ Hyett, and Tom Preston-Werner. GitHub offers both commercial plans and free accounts for open source projects. According to the Git User&#8217;s Survey in 2009, GitHub is the most popular Git hosting site.[2]
> 
> The site provides social networking functionality like feeds, followers and the network graph to display how developers work on their versions of a repository.
> 
> GitHub also operates a pastebin-style site, wikis for the individual repositories and web pages that can be edited through a git repository.
> 
> As of January 2010[update], GitHub is operated under the name GitHub, Inc

## TortoiseGit

I choose to install [TortoiseGit][5] to use as a client. But first you need to install [PuTTy][6]. And then you need to install [msysgit][7]. I installed the following version [Git-1.7.0.2-preview20100309][8]. 

[I was helped by this article.][9]

Select the option Add “Git Bash here”

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github1.png" alt="" title="" width="513" height="398" />
</div>

Select the option Run Git from the Windows Command Prompt

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github2.png" alt="" title="" width="513" height="398" />
</div>

Select the option Use Windows style line endings

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github3.png" alt="" title="" width="513" height="398" />
</div>

If it asks for PLink, locate plink.exe in your PuTTY install directory (e.g. C:Program FilesPuTTYplink.exe)

Then I installed TortoiseGit and rebooted.

## SSH keys

GitHub uses SSH and so you need to generate keys. That&#8217;s where PuTTy comes in. You use PuttyGen to Generate a key pair. Just click on the Generate button and move the mouse a little. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github4.png" alt="" title="" width="493" height="477" />
</div>

Then Save the private key somewhere you can easily remember where you put it. And copy the text from the top textbox. Make sure you keep this text until you need it later on.

Now also add the key to pageant. Just do add key and point it to the private key file you saved.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github5.png" alt="" title="" width="511" height="363" />
</div>

## GitHub

Now you have to go to Github and create an account. Once you have your account you should first add the public key. You can add a public key in your account settings. You give it a name and you just copy in the public you copied a little earlier.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github6.png" alt="" title="" width="697" height="251" />
</div>

Now that you have all this setup you can go to the http://github.com/structuremap/structuremap and click on the fork button. This will create a fork in your account. You can now go to you account and see the url you need to download it to you local repository. 

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github7.png" alt="" title="" width="930" height="230" />
</div>

You should now create a directory where you want to save you sourcecode and then you right click on a directory somewhere and select TortoiseGit -> Settings. Then in Git -> config you set your name and emailaddress, the one you used to create your Github account.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github8.png" alt="" title="" width="709" height="474" />
</div>

## Clone

Then I decided to clone my github fork to my local repository. You give it the git url that you can copy paste from the github page. You set your local repository. And you point it to the private key file we created earlier.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github9.png" alt="" title="" width="485" height="364" />
</div>

For NSubstitute I just used the read only url and did not fork it since I don&#8217;t really need to change that code for now.

<div class="image_block">
  <img src="/wp-content/uploads/blogs/DesktopDev/github/Github10.png" alt="" title="" width="485" height="364" />
</div>

## Conclusion

Well it was a lot of work for something so simple. Downloading all the files and installing them will take some time. Configuring everything is pretty easy and everything works pretty smooth if you don&#8217;t make any mistakes. But if you do make a mistake then be prepared to lose some time since error messages don&#8217;t always say what they should.

 [1]: http://nsubstitute.github.com/
 [2]: http://structuremap.github.com/structuremap/index.html
 [3]: https://github.com/
 [4]: http://en.wikipedia.org/wiki/GitHub
 [5]: http://code.google.com/p/tortoisegit/
 [6]: http://the.earth.li/~sgtatham/putty/latest/x86/putty-0.60-installer.exe
 [7]: http://code.google.com/p/msysgit/downloads/list
 [8]: http://code.google.com/p/msysgit/downloads/detail?name=Git-1.7.0.2-preview20100309.exe&can=2&q=
 [9]: http://wiki.github.com/multitheftauto/multitheftauto/how-to-use-tortoisegit