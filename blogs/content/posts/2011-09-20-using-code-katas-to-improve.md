---
title: Using Code Katas to Improve Programming Skills
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-09-20T09:56:00+00:00
excerpt: "Feelings about programming katas tend to fall into one of 3 buckets; disdain, indifference, or appreciation. The indifferent crowd generally doesn't see what you learn from repeating the same coding exercise, while the disdainful crowd feels the same way, but at a much louder (and of course disdainful) volume. That leaves the group that feel there is value in repetitively coding the same exercise or coding numerous small code examples."
url: /index.php/itprofessionals/professionaldevelopment/using-code-katas-to-improve/
views:
  - 31159
rp4wp_auto_linked:
  - 1
categories:
  - Professional Development
tags:
  - development
  - kata
  - programming

---
Feelings about programming katas tend to fall into one of 3 buckets; disdain, indifference, or appreciation. The indifferent crowd generally doesn&#8217;t see what you learn from repeating the same coding exercise, while the disdainful crowd feels the same way, but at a much louder (and of course disdainful) volume. That leaves the group that feel there is value in repetitively coding the same exercise or coding numerous small code examples.

I was in the indifferent group, but then I tried it.

## Where I Started

<img src="http://tiernok.com/LTDBlog/clock.png" style="float: right; margin: 0px 0px 10px 10px" />

After finishing [The Clean Coder][1] recently, I decided to give this kata thing a try. I started out with a fairly simple problem in a language I was comfortable with and spent 15-30 minutes each night trying a different variant or constraint. What I found was that even the act of setting up for a kata was a learning experience, in one case learning an entirely new unit test framework before I could even begin.

Fast forward a couple weeks and I think this is becoming a regular part of my ongoing development. I&#8217;ve even taken to synching my local mercurial repositories up to [my BitBucket account][2] so that others can take advantage of the pre-setup projects and test scenarios. (I haven&#8217;t quite embraced the throw-away aspect yet, but I suspect I won&#8217;t be keeping all of my repetitive attempts.)

In the past several weeks I&#8217;ve gained a lot more practice with mercurial, played with some concepts in C# (like Continuation Passing Style), and started digging into unit testing JavaScript. I&#8217;ve learned markdown simply by spending an hour or two formatting a series of text documents (an uninspired exercise, I&#8217;ll admit) and .. well, for the not much time investment I can already see that this will be a good habit to add.

## Why (or How) it Works

So why, then, do people do this? Obviously it takes time to write a program, no matter how small. [Robert C. Martin][3], author of The Clean Coder above, swears by this practice. Jeff Atwood is another who [wrote about katas][4]. Dave Thomas (first kata link below) introduced the term as co-author of The Pragmatic Programmer. 

But where is the value in repeating a simple exercise?

### The Challenge

The first thing that struck me about code katas is their similarity to forum questions the LTD group used to hijack, back in the day. Someone would come up with a perfectly reasonable solution to a somewhat reasonable problem, then 2 or 3 of us would turn it into a 30 reply post, each trying to find a shorter, faster, whackier, whatever-er method of solving the same problem. In fact, the reason I learned how to use python and perl in classic ASP web pages was due to challenges like this.

The value of short, varied approaches is it forces us to stretch our knowledge of a language or syntax. Playing with constraints (performance, code length, memory limits, etc) help us explore more about a language or technology and choosing a short exercise as a base means we spend most of our time in that exploration, rather than in the act of retyping a solution or having to build a large structure to work with.

### Focus

<img src="http://tiernok.com/LTDBlog/Scores2.png" style="float: right; margin: 0px 0px 10px 10px" />

By selecting a fairly simple problem, we can choose to focus on any other aspect of programming that we want. Besides introducing rules or constraints to make the program more challenging, we can also use repetition of a simple, known problem to better get to know our tools or learn new supporting tools. Examples include improving use of keyboard shortcuts, trying out methods like TDD, and training ourselves on new habits or standards. By working on something we already know the answer to, we are free to play with the aspects of how we get to that answer.

### No Loss

The last benefit to a programming kata is we can throw it away when we&#8217;re done or even stop it halfway and simply delete it. Whatever aspect we have chosen to focus on or challenge ourselves with, this is still a small bit of practice code. Rather than trying to learn something new with a piece of code we will eventually have to support, we can play in a sandbox and put it away when we&#8217;re done.

## Where Can You Start

So, if you wanted to try this out, where could you start?

  * [CodeKata.PragProg.com][5] &#8211; Dave Thomas has listed 21 programming and non-programming exercises
  * Forums &#8211; Find a forum question that requires more than 5 lines of code for an answer. Write it, reinvent it, mix, repeat
  * [Coding Dojo][6] &#8211; A catalog of 23 katas
  * [Project Euler][7] &#8211; a series of challenging math problems that make excellent practice problems
  * [TopCoder][8] &#8211; Though it exists as a programming competition, the sample and practice problems at TopCoder make good katas (if you can get the crash-friendly interface to work and find them, that is)
  * Blogging &#8211; Writing about a topic forces us to explore and better define or communicate it

The best way to start is to pick something you feel like you can finish in 15-30 minutes and try to do a variant of it every day or two. If you feel like you&#8217;re not getting any value out of it after a week or two, you can stop; your time investment has been low. If you do start seeing some value in it, though, you have another tool for keeping your skills relevant and sharp.

 [1]: http://www.amazon.com/gp/product/0137081073 "The Clean Coder at Amazon"
 [2]: http://bitbucket.org/tarwn "Go to my BitBucket account"
 [3]: http://www.objectmentor.com/omTeam/martin_r.html
 [4]: http://www.codinghorror.com/blog/2008/06/the-ultimate-code-kata.html
 [5]: http://codekata.pragprog.com/
 [6]: http://codingdojo.org/cgi-bin/wiki.pl?KataCatalogue
 [7]: http://projecteuler.net/
 [8]: http://topcoder.com/