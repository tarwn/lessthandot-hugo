---
title: Writing a plugin for b2evo
author: Christiaan Baes (chrissie1)
type: post
date: 2009-01-05T07:04:57+00:00
ID: 267
url: /index.php/webdev/serverprogramming/writting-a-plugin-for-b2evo/
views:
  - 5557
categories:
  - PHP
  - Server Programming
tags:
  - b2evo
  - b2evolution
  - pluin

---
Last Friday, I got a request from one of our bloggers to have a means of getting a list of his posts. I thought that this couldn&#8217;t be that hard. I was slightly wrong. First, I thought I would do it all myself and use a direct connection to the database to get the data needed. But then I looked a bit closer at [b2evo][1] (the engine we use) and saw that it already had a link called [archives][2] and I thought that it would be easier to just change that.
  
So first I went on the lookout for the file that generated the archives and it turned out to be in the plug-ins folder called \_archives.plugin.php. Cool. I just have to copy paste the file and rename it to \_authors.plugin.php and I would have the code to work on and make the changes. Which I did, swiftly. It is all pretty reasonable and understandable. I just had to change some of the sql. But the code works for two things, one it is a widget and two it can be used to generate a page. So making the list of authors with how many posts they made was pretty easy. And then I noticed that the archives page is called by using a disp parameter namely ?disp=arcdir and that didn&#8217;t really work for ?disp=authdir. So I went to look for this variable. First I found a file in the skins folder named \_arcdir.disp.php which seemed close enough for what I want. But that wasn&#8217;t enough I also had to add a little bit in the \_skin.func.php file so that the new parameter would be accepted (I think they should change this and just scan the files in the skins folder called disp). All in all, it was pretty painless and the code is reasonably self-explanatory. Getting the right fields out of the database took some investigating but nothing I couldn&#8217;t handle.

So I think that writing a plug-in for b2evo could be a bit better and more streamlined but it is getting there. 

You can find the source code in the [b2evolution forums][3]. 

You can see the plug-in in action [here][4] and you can see the widget on the right menubar.

Apparently you can also do [this][5] in b2evo. But I don&#8217;t think I want our users to remember the user_Id of every author. But I think I can add it as an extra feature.

 [1]: http://b2evolution.net/
 [2]: /index.php/All/?disp=arcdir
 [3]: http://forums.b2evolution.net/viewtopic.php?p=85192
 [4]: /index.php/All/?disp=authdir
 [5]: /?author=7