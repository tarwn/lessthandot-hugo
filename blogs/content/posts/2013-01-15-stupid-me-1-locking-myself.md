---
title: 'Stupid me #1 – Locking myself out of SQL Server'
author: Koen Verbeeck
type: post
date: 2013-01-15T11:07:00+00:00
ID: 1917
excerpt: The first blog post of a possible series where I describe a little problem I encountered and how I solved it.
url: /index.php/datamgmt/dbprogramming/mssqlserver/stupid-me-1-locking-myself/
views:
  - 10685
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server
tags:
  - default database
  - locked out
  - sa account
  - sql server

---
<p class="MsoNormal" style="text-align: justify; text-justify: inter-ideograph;">
  <span lang="EN-US">I've been thinking lately to do some sort of blog post series. Every time I do something "stupid", which happens from time to time, I'll do a little blog post on what happened and how I solved it. The reason for this is twofold: I'll have a solution online I can consult if it happens again and other people can benefit from my mistakes as well. Because remember the ancient Chinese proverb: <em>"It's only stupid if you don't turn it into a learning experience"</em>. Okay, I might have made that last one up...</span>
</p>

<p class="MsoNormal" style="text-align: justify; text-justify: inter-ideograph;">
  <strong><span lang="EN-US">The problem</span></strong>
</p>

<p class="MsoNormal" style="text-align: justify; text-justify: inter-ideograph;">
  <span lang="EN-US">My first <em>Stupid-Me</em> (patent pending) is how I recently locked myself out of SQL Server. What happened? I installed a SQL Server instance on my local development machine so I could test out some small scripts. Typically stuff I'm researching or when I'm writing queries to answer some questions on sqlservercentral.com. In this instance I have a database called <em>Test</em> (what's in a name?) in which I execute most of my queries. To save myself the trouble of changing the database scope to the Test database every time I log into the server and open a query window, I made this database my default database for my domain login. Everything fine so far.</span>
</p>

<p class="MsoNormal" style="text-align: center;">
  <a href="/media/users/koenverbeeck/StupidMe1/login.png?mtime=1358236062"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/StupidMe1/login.png?mtime=1358236062" alt="" width="492" height="442" /></a>
</p>

<span style="text-align: justify;">After a couple of months, the database was getting cluttered with all kinds of test tables, stored procedures, views et cetera. Time to clean ship I thought and I dropped the Test database, with the idea of just creating a new one. First I got this lovely message:</span>

<p class="MsoNormal" style="text-align: justify; text-justify: inter-ideograph;">
  <span lang="EN-US"> </span>
</p>

<div class="image_block">
  <a href="/media/users/koenverbeeck/StupidMe1/error_inuse.png?mtime=1358236018"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/StupidMe1/error_inuse.png?mtime=1358236018" alt="" width="560" height="427" /></a>
</div>

<span style="text-align: justify;">I thought one of my query windows was still open with an active connection to the database, so being the smarty pants that I am I ticked the checkbox </span>_"Close existing connections"_ <span style="text-align: justify;">without giving it too much thought. The drop database succeeded, but when I tried to create a new database, I got this beauty on my screen:</span>

<p class="MsoNormal" style="text-align: justify; text-justify: inter-ideograph;">
  <span lang="EN-US"> </span>
</p>

<div class="image_block">
  <a href="/media/users/koenverbeeck/StupidMe1/error1.png?mtime=1358235987"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/StupidMe1/error1.png?mtime=1358235987" alt="" width="536" height="177" /></a>
</div>

<span style="text-align: justify;">Whoops. I dropped my default database. Logging out and in didn't work:</span>

<p class="MsoNormal" style="text-align: justify; text-justify: inter-ideograph;">
  <span lang="EN-US"> </span>
</p>

<div class="image_block">
  <div class="image_block">
    <a href="/media/users/koenverbeeck/StupidMe1/error2.png?mtime=1358235996"><img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/koenverbeeck/StupidMe1/error2.png?mtime=1358235996" alt="" width="536" height="144" /></a>
  </div>
</div>

<p style="text-align: justify;">
  I couldn't get back in my own SQL Server instance!
</p>

<p class="MsoNormal" style="text-align: justify;">
  <strong><span lang="EN-US">The solution</span></strong>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">So what did I do? Luckily I had configured the SQL instance to use mixed authentication mode and I had defined an SA account during the set-up. I simply logged in using the SA account, changed the default database of my domain login and behold, I could log in again. An alternative would be to create a new database with the same name as your too soon departed default database.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US">Creating an SA account – with a secure password of course – is a handy solution to prevent you from locking yourself truly out of SQL Server. A few years back I didn't have an SA account and I did lock myself out because of domain issues. I had to fall back to methods described in this MSDN page: </span><a href="http://msdn.microsoft.com/en-us/library/dd207004.aspx"><span lang="EN-US">Connect to SQL Server When System Administrators Are Locked Out</span></a><span lang="EN-US">. Configuring SQL Server to start up using single-user mode and making sure you are the only user is a bit trickier than just using the SA account to log in.</span>
</p>

<p class="MsoNormal" style="text-align: justify;">
  <span lang="EN-US"><strong>Edit: </strong>Be sure to check out the comments below. There are some interesting links to alternative methods.</span>
</p>