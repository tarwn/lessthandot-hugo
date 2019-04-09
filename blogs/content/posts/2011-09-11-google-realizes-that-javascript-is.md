---
title: Google realizes that JavaScript is a dead end programming language, to be replaced by Dart a new language
author: SQLDenis
type: post
date: 2011-09-11T20:57:00+00:00
ID: 1313
excerpt: |
  All the way back in 2010 Google realized that JavaScript is a dead end programming language.
  
  Here is what was posted on the com.googlegroups.google-caja-discuss list
  
  Javascript has fundamental flaws that cannot be fixed merely by evolving the
  lan&hellip;
url: /index.php/webdev/webdesigngraphicsstyling/google-realizes-that-javascript-is/
views:
  - 9310
rp4wp_auto_linked:
  - 1
categories:
  - AJAX
  - Javascript
  - Web Design, Graphics and Styling
tags:
  - dart
  - javascript
  - programming
  - web

---
All the way back in 2010 Google realized that JavaScript is a dead end programming language.

Here is what was posted on the [com.googlegroups.google-caja-discuss][1] list

> Javascript has fundamental flaws that cannot be fixed merely by evolving the
  
> language. We'll adopt a two-pronged strategy for the future of Javascript:
> 
> &#8211; Harmony (low risk/low reward): continue working in conjunction with
     
> TC39 (the EcmaScript standards body) to evolve Javascript
     
> &#8211; Dash (high risk/high reward): Develop a new language (called Dash) that
     
> aims to maintain the dynamic nature of Javascript but have a better
     
> performance profile and be amenable to tooling for large projects. Push for
     
> Dash to become an open standard and be adopted by other browsers. Developers
     
> using Dash tooling will be able to use a cross-compiler to target Javascript
     
> for browsers that do not support Dash natively.

A new language will be announced at the goto conference named Dart, the keynote will be: [Opening Keynote: Dart, a new programming language for structured web programming][2]

Assuming that Dash is now called Dart, here is what it is supposed to do, again according to the posting on the com.googlegroups.google-caja-discuss list

> Dash is the leapfrog effort that is designed to be a clean break from
  
> Javascript. It will seek to keep the parts that have made the Internet so
  
> successful, but fill in holes everyone agrees it has.
> 
> Dash is designed with three perspectives in mind:
> 
> &#8211; Performance &#8212; Dash is designed with performance characteristics in
     
> mind, so that it is possible to create VMs that do not have the performance
     
> problems that all EcmaScript VMs must have.
     
> &#8211; Developer Usability &#8212; Dash is designed to keep the dynamic,
     
> easy-to-get-started, no-compile nature of Javascript that has made the web
     
> platform the clear winner for hobbyist developers.
     
> &#8211; Ability to be Tooled &#8212; Dash is designed to be more easily tooled (e.g.
     
> with optional types) for large-scale projects that require
     
> code-comprehension features such as refactoring and finding callsites.
      
> Dash, however, does not require tooling to be effective&#8211;small-scale
     
> developers may still be satisfied with a text editor.
> 
> Dash is also designed to be securable, where that ability does not seriously
  
> conflict with the three main goals.
> 
> Dash will be designed to be consumed in a number of locations:
> 
> &#8211; Browser VM &#8212; Our aspiration is that Dash will ultimately be a viable
     
> substitute for Javascript as the native client-side language of choice
     
> across all browsers.
     
> &#8211; Front-end Server &#8212; Dash will be designed as a language that can be
     
> used server-side for things up to the size of Google-scale Front Ends. This
     
> will allow large scale applications to unify on a single language for client
     
> and front end code.
     
> &#8211; Dash Cross Compiler &#8212; Dash will be designed so that a large subset of
     
> it can be compiled to target legacy Javascript platforms so teams that
     
> commit to using Dash do not have to seriously limit their reach. Platforms
     
> that have a Dash VM can operate on the original Dash code without
     
> translation and take advantage of the increased performance. One of the
     
> ways we will evolve Harmony is to be a better target for such compiled Dash
     
> code.

So far we don't know much about this new language except for what they say about Dash, we can assume that it will address some of the points that were posted in the list which I shared in this post earlier

Even if this new language is better than JavaScript will all the other browser vendors be on board? Remember that Google launched the [Go language][3] last year, what happened to that?

I am skeptical about all of this but of course I could be dead wrong

Time will tell………

 [1]: http://markmail.org/message/uro3jtoitlmq6x7t
 [2]: http://gotocon.com/aarhus-2011/presentation/Opening%20Keynote:%20Dart,%20a%20new%20programming%20language%20for%20structured%20web%20programming
 [3]: http://golang.org/