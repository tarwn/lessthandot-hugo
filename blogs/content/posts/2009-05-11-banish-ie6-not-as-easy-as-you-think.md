---
title: Banish IE6? Not As Easy As You Think
author: SQLDenis
type: post
date: 2009-05-11T12:17:44+00:00
url: /index.php/itprofessionals/ethicsit/banish-ie6-not-as-easy-as-you-think/
views:
  - 3461
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
tags:
  - ie
  - internet explorer
  - windows 7

---
You have all seen sites that popup messages telling you that your IE6 browser is insecure and that you should upgrade to IE7/8, FireFox or Chrome. The reason for this is of course that IE6 is a terrible browser to develop against; nobody wants to spend half their time putting in CSS hacks so that their webpage looks halfway decent. Some sites like [37signals decided to dump IE6][1] completely 

So here is the problem with this, most companies I interact with won’t let you run as an administrator and you cannot install a single thing. Yeah you can download another browser but you can’t install it. Most of these companies also have windows update disabled on the machines, updates are pushed through LANDesk. A new browser is not part of these update pushes. Just imagine that you call help desk to ask them to install a new browser on your machine. Most probably you will get an answer that IE6 is the corporate standard and that it is the browser you have to use.

Of course some companies developed internal apps that use ActiveX and will only work on IE6, this is another reason that IE6 will not go away any time soon.

On our site IE6 still commands about 30% usage of total IE usage. Here is the breakdown for the last 10 months

<pre>Aug 2008 30.06%
Sep 2008 31.14%
Oct 2008 30.10%
Nov 2008 28.62%
Dec 2008 28.95%
Jan 2009 29.74%
Feb 2009 30.38%
Mar 2009 29.73%
Apr 2009 25.90%
May 2009 24.76%</pre>

As you can see it was around 30% until April and then it dropped. This drop is probably because of the introduction of IE8 in April at Mix09

Here is all IE for May

<pre>IE7 57.73%	
IE6 24.76%	
IE8 17.51%</pre>

As you can see IE8 is closing in on IE6 fast, it is however also taking market share away from IE7

It will be interesting to see how many people will upgrade to Windows 7 this year, so far XP is still king For the month of May 2009 XP traffic was 68.24% of all windows machines on our site

 [1]: http://37signals.blogs.com/products/2008/07/basecamp-phasin.html