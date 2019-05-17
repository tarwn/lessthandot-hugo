---
title: Solving Sudoku in Silverlight
author: ThatRickGuy
type: post
date: 2010-04-09T14:37:24+00:00
ID: 672
url: /index.php/webdev/webdesigngraphicsstyling/solving-sudoku-in-silverlight/
views:
  - 2442
rp4wp_auto_linked:
  - 1
categories:
  - Web Design, Graphics and Styling
tags:
  - silverlight sudoku vb.net

---
Long ago I came across [OldSchoolDotNet's Silverlight Sudoku client][1]. 

It's a great interface to a classic brain game. I especially enjoy the "Hard" difficulty level as it is pretty consistently solvable with out reverting to guessing. The "Expert" level though requires you to guess. And as a logic guy, I hate guessing. 

Not quite so long ago, in the [Programmer's Puzzles forum][2], we had a "Follow the Clues" challenge. I approached this problem just as I do Sudoku. Writing out the possibilities and eliminating the incorrect answers as I went through the rules list over and over. But just solving the problem isn't enough, the goal, seeing as how most of us are coders of one sort or another, is to write a software solution. So I put together a system that given an array of possibilities and a set of rules, would iterate through the rules and grid filtering out all impossible answers till only the correct ones existed.

It was a fun project, but it made me think. I was solving that problem in roughly the same way I approach Sudoku. So why not improve my solution to be able to solve Sudoku challenges as well?

There are a set number of rules to Sudoku, and a number of patterns that we can see that make solving them easier. So it was just a matter of coding out these rules and inferences, and applying them to a 3-dimensional array of Boolean values (column, row, index).

My initial attempts worked perfectly for anything up to the "Hard" level from OldSchoolDotNet's Sudoku client. But on the "Expert" level, it would get through all of the known steps and then stop. Adam came up with an idea for how to handling the guesses in a tree like branching method. And after a few attempts, I managed to come up with a decent solution.

It isn't cleaned up at all, but if you're stuck on a sudoku puzzle, it may be of use to you. Each click of the "Apply Rules" button will perform one pass of each rule (horizontal, vertical, block) on each square in the board. With out guessing, most puzzles can be solved in about 6-10 passes. 

With guessing, it'll take a while. My last solving of an "Expert" puzzle wound up fielding over 18,000 branches. As the number of active branches increases, performance will degrade. But once you get through the bulk of them, the performance will increase as more and more branches are closed.

So, if you're a Sudoku fan, check it out [Here][3].

Code is available [Here][4]

-Rick

 [1]: http://oldschooldotnet.blogspot.com/2009/03/sudoku-in-silverlight.html
 [2]: http://forum.lessthandot.com/viewforum.php?f=102
 [3]: http://ringdev.com.web10.reliabledomainspace.com/code/sodukusolver/index.html
 [4]: http://ringdev.com.web10.reliabledomainspace.com/code/sodukusolver/SodukuSolver.zip