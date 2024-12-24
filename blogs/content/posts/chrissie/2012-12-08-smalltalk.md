---
title: Smalltalk
author: Christiaan Baes (chrissie1)
type: post
date: 2012-12-08T06:24:00+00:00
ID: 1835
excerpt: Discovering smalltalk.
url: /index.php/desktopdev/generalpurposelanguages/smalltalk/
views:
  - 5294
categories:
  - General Purpose Languages
tags:
  - smalltalk

---
## Introduction

A few days ago [Demis Bellot][1] (the creator of Servicestack) was talking about [Smalltalk][2] on twitter. A little discussion came about involving [Alex Ullrich][3].

<div class="image_block">
  <a href="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/smalltalk/smalltalk1.png?mtime=1354953430"><img alt="" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/users/chrissie1/smalltalk/smalltalk1.png?mtime=1354953430" width="299" height="718" /></a>
</div>

So my next mission was to learn a thing or two about smalltalk myself. 

## Installation

I tried two smalltalk IDE&#8217;s. [Squeak][4] (which I couldn&#8217;t get to work on Win8) and [Dolphin X6 community edition][5].

So I stuck with Dolphin. And the installation is next, next, next and finish.

## The beginning

So how does one learn Smalltalk, on starts with finding a good tutorial. And I found a very good one by Bitwise magazine. 

[Lesson 1][6] has Dolphin and Squeak in it but from [Lesson 2][7] you can go to a dedicated Dolphin page(s).

Just follow that tutorial and you will be fine. 

To be honest, smalltalk is weird. I have done a few languages in my time, lots of basic dialects (visual basic, turbobasic VBA, &#8230;), lots of c dialects (C++, Java, C#, javascript, PHP, &#8230;), COBOL, Delphi, and some I have forgotten what the syntax looks like. Smalltalk however is nothing like the previous languages. 

It uses the comma to concatenate strings.

```smalltalk
'Hello',' World!'.```
It uses the dot as a line-ending character.

It uses square brackets for blocks of code.

```smalltalk
1 timesRepeat: [Transcript clear.
Transcript show: 'Hello'; cr.
Transcript show: 'Hello2'; cr.].```
It uses the space as a seperator character to define arrays.

```smalltalk
mixedlist := #( 'one' 2 (3 4 5) $6 ).```
It uses capitalization to determine scope, variables starting with an uppercase letter are Global scope, variables with a lowercase are local to your workspace.

And there are many more things you will discover. 

Learning something like smalltalk is really cool. It opens your eyes a bit. 

## Conclusion

Go ahead, download dolphin and follow the bitwise tutorial, you will learn something new if you have never tried smalltalk before.

 [1]: https://twitter.com/demisbellot
 [2]: http://en.wikipedia.org/wiki/Smalltalk
 [3]: /index.php/All/?disp=authdir&author=5
 [4]: http://en.wikipedia.org/wiki/Squeak
 [5]: http://www.object-arts.com/
 [6]: http://www.bitwisemag.com/copy/programming/smalltalk/smalltalk1.html
 [7]: http://www.bitwisemag.com/copy/programming/smalltalk/dolphintutorial.html