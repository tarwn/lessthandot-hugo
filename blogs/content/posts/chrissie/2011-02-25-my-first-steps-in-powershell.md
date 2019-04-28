---
title: My first steps in powershell and stopping and starting a process
author: Christiaan Baes (chrissie1)
type: post
date: 2011-02-25T06:56:00+00:00
ID: 1051
excerpt: |
  Introduction
  
  Yesterday I had the brilliant idea to automate the checkout of a git branch. As you might know when you work with Visual studio and and git, you need to best close VS before you do a checkout and then you have to restart it. All this see&hellip;
url: /index.php/desktopdev/generalpurposelanguages/my-first-steps-in-powershell/
views:
  - 4210
categories:
  - General Purpose Languages
tags:
  - devenv
  - powershell

---
## Introduction

Yesterday I had the brilliant idea to automate the checkout of a git branch. As you might know, when you work with Visual studio and git, you need to close VS before you do a checkout and then you have to restart it. For the people that don&#8217;t know, Visual studio will start asking to reload your projects one at a time which can take many clicks, it is also possible that if you leave a certain tab open with a file that is not in the other branch that this tab remains open and if you do save all you might end up with this file in the wrong branch. All this seemed ideal for a little script. Little did I know the trouble I would get myself into, but I will report about this in my next post. 

## The beginning

First thing to do was to find the open devenv processes.

This is simple 

```
Get-Process devenv ```
OK it isn&#8217;t. Get-process devenv will give you either an object of type process or an array of processes, depending if you have one or more processes of type devenv. This does not make for easy programming but I can live with it. 

So if you do this

```
$processes = Get-Process devenv 
write-host $processes.Count```
you will get no output if you have one devenv open or a number if you have several open. You will get an exception when there is no devenv. 

So lets move on and imagine there are several devenv&#8217;s open we can do something like this to get a list of devenv&#8217;s.

```
if($processes.Count -gt 0)
{
    $i = 0
    Write-host "There are multiple processes for devenv."
    foreach($process in $processes)
    {
        $i++
        $i.ToString() + '. ' + $process.MainWindowTitle
    }
}```
Which gave me this.

> **2
  
> There are multiple processes for devenv.
  
> 1. ConsoleApplication1 &#8211; Microsoft Visual Studio (Administrator)
  
> 2. ConsoleApplication2 &#8211; Microsoft Visual Studio (Administrator)**

Then I wanted to pick one of the two processes so I could close and reopen just the one I was working on. Or if there is just one than I can just close and reopen that.

Next step is to take input.

Taking input is easy with read-host.

```
$in = Read-host "Give a number of the process to kill: "```
and now I can just pick the process I want.

```
write-host "killing and restarting: " + $processes[$in-1].MainWindowTitle
    ```
That has the desired result.

```
killing and restarting:  + ConsoleApplication1 - Microsoft Visual Studio (Administrator)
```
Stopping the process is fairly easy.

```
$processes[$in-1].Kill()
$processes[$in-1].WaitForExit()
```
That is the more friendly version of kill, you can also force it to kill in a less friendly manner.

So I&#8217;m nearly there I Guess. Now I just have to start the process up again.

That should be as simple as this.

```
$processes[$in-1].Start()```
Nope it isn&#8217;t since that 

> <span class="MT_red">Exception calling &#8220;Start&#8221; with &#8220;1&#8221; argument(s): &#8220;Cannot start process because a file name has not been provided.&#8221;</span>

I could now start to explain what I tried to get this going but it would take to long. I even posted on [StackOverflow][1] and asked the help of Aaron Nelson ([blog][2]|[twitter][3]) and I got close but not close enough.

The big problem is is that the process does not know what project/solution devenv has open so I can&#8217;t tell it to open that. I can tell it to open devenv again but that is not what I am after. 

## Conclusion

I failed (lucky I&#8217;m not a samurai else I would have had to commit suicide, although not everybody will think so). I could not get it to work in a reasonable time so I gave up on it, perhaps if I dig around some more I could find a possibility but I&#8217;m pretty sure that it is not worth it. But I had fun and learned a lot.

 [1]: http://stackoverflow.com/questions/5091345/stop-and-then-start-a-process-in-powershell
 [2]: http://sqlvariant.com/wordpress/
 [3]: http://twitter.com/#!/SQLvariant