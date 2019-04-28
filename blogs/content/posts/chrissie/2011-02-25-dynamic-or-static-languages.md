---
title: Dynamic or static languages
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-25T18:57:00+00:00
ID: 1055
excerpt: |
  Yesterday I was working in Powershell, which is mostly dynamic in nature and I have also worked a bit with PHP (for this site). For my daily work I use VB.Net and C# which are mostly static or I tend to use the static parts of them.
   
  So let's go back&hellip;
url: /index.php/itprofessionals/professionaldevelopment/dynamic-or-static-languages/
views:
  - 1238
categories:
  - Professional Development
tags:
  - dynamic
  - static

---
Yesterday I was working in Powershell, which is mostly dynamic in nature and I have also worked a bit with PHP (for this site). For my daily work I use VB.Net and C# which are mostly static or I tend to use the static parts of them.

 

So let&#8217;s go back to yesterday where I was working with Powershell, sorry I have a very short attention span and my fingers don&#8217;t type as quick as my mind things so I might seem a bit erratic, which I&#8217;m not. It just seems like I&#8217;m erratic.

So back to yesterday again where I was working with Powershell and I did this.

```
$process = Get-Process devenv```
I see 2 problems with the above. First, I don&#8217;t know what process is going to be at design time, I only know what it is going to be at runtime. $process can be an object of type process if there is only one process of type devenv or it can be an array of type process when there are several devenv processes.

This means that I have to do some extra thinking before writing something like that.
  
I can also remember one time when I was working with PHP and I was using a function someone else wrote. I used it without looking at the code and just went by the name of the method. I thought the name said enough and I was pretty sure I knew what it was going to return, and that something was supposed to be a number. The problem was that it did not always return a number. It sometimes returned a string with &#8220;false&#8221; in it. This to me was a complete surprise.
  
That brings me to the second, much smaller problem, and that is that the naming of methods and variables is much more difficult. Should I have called the above $process or $processes? Both would be fine I guess but neither really covers the whole intention. This will make it harder to read and understand for the person that has to maintain it after you. 

Of course, before you start moaning and getting rowdy, the solution is simple. The solution are unit tests. TDD and BDD are an absolute must in my eye when you work with dynamic language just to avoid stupid bugs and sometimes easily overlook-able bugs.

look at this PHP-statement 

```
$test = 0;
$test += 1;
$tes += 2```
PHP will happily make you a second variable called $tes while I did not mean to. The only way to avoid stupid mistakes like that is to have tests. Test, tests and more tests. 

In static languages you will not have the problems I presented above. You will have some different problems but testing is still important.

So I have come to the conclusion that programming in a dynamic language without good automated tests is crazy stuff and bound to have more of the bugs I explained above, just the kind of bugs static languages don&#8217;t have.

Of course Powershell is a scripting language and and unit testing is not something you will do for a simple script. But perhaps now is a good time to start.

Just my 2cents worth.