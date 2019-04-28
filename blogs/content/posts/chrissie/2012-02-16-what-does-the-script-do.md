---
title: 'What does the script: do in powershell when used with a function.'
author: Christiaan Baes (chrissie1)
type: post
date: 2012-02-16T06:40:00+00:00
ID: 1538
excerpt: 'Where I try to found what the script: means when used when declaring a function. And find it unuseful.'
url: /index.php/desktopdev/mstech/what-does-the-script-do/
views:
  - 1441
categories:
  - Microsoft Technologies
tags:
  - powershell
  - script scope

---
I was looking at the posh-git code and I found the author doing this.

```
function script:gitCmdOperations($command, $filter)```
Needless to say I didn&#8217;t find anything on google about this, because google ignored the : and looking for script in a scripting language gives you lots of hits.

I then asked the question on twitter and after that [I posted the question Stackoverflow][1].

And got this reply from [user Rynant][2].

> It defines the function&#8217;s scope to be the script scope. See: help about_scopes

Here is the complete text of [help about_scopes][3]

So I did that and found this.

> Script:
       
> The scope that is created while a script file runs. Only
       
> the commands in the script run in the script scope. To
       
> the commands in a script, the script scope is the local
       
> scope.

Kaboom, head explodes.

But in short you can change the scope of a function by adding a scope modifier. Which is more usefull if you start nesting scopes. Like [Shay Levy][4] explains in his answer.

And user [JasonMArcher][5] adds to that.

> It should be noted that script: scope doesn&#8217;t make any difference for functions that are right in the script. But it does make a different for nested functions.

And that&#8217;s when it started to make sense to me, kind of.

So if my understanding is correct than it doesn&#8217;t do much in the case of the posh-git code. But you can prove me wrong if you so desire.

 [1]: http://stackoverflow.com/questions/9296122/powershell-what-does-function-script-functionname-do-especially-the-script-pa
 [2]: http://stackoverflow.com/users/291709/rynant
 [3]: http://technet.microsoft.com/en-us/library/dd315289.aspx
 [4]: http://stackoverflow.com/users/9833/shay-levy
 [5]: http://stackoverflow.com/users/64046/jasonmarcher