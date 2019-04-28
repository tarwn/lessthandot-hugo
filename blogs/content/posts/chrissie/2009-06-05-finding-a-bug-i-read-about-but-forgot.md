---
title: Finding a bug I read about but forgot
author: Christiaan Baes (chrissie1)
type: post
date: 2009-06-05T05:12:19+00:00
ID: 456
url: /index.php/desktopdev/mstech/finding-a-bug-i-read-about-but-forgot/
views:
  - 6123
categories:
  - Microsoft Technologies
tags:
  - rhino mocks
  - visualstudio 2008

---
Yesterday I had a bad day. My build on the buildserver wouldn&#8217;t run. For some mysterious reason VS decided to crash when the solution was opened with the very famous &#8220;System Error &He06d7363&&#8221; which even Google can&#8217;t seem to find. My build machine happens to be on XP (VM) and my developing machine is on Vista 64 bits. 

So after searching for at least half a day, I got the brilliant idea of reinstalling the VS2008 SP1 and that fixed the problem ;-). I asked no further questions, it just did.

But now that it would build again, it started showing some weird behaviour in my unit tests. With even weirder error messages. Mostly very vague, to say the least. So I had to investigate the problem. Using the nunitconsole, resharpers testrunner, the debugger and a lot of bad words. I got to the conclusion that it was a call to Rhino mocks that did it, but not all calls, just a few. 

[But I read about this a while ago][1]. Strange thing is that that code had been building fine up until now. I wonder what changed to trigger it? So I installed the [fix][2].

> An unhandled exception of type &#8216;System.ExecutionEngineException&#8217; occurred in mscorlib.dll

After reading that I even checked the event viewer and lo and behold, I found one. Well a few dozen to be exact. 

> Event Type: Error
  
> Event Source: .NET Runtime 2.0 Error Reporting
  
> Event Category: None
  
> Event ID: 1000
  
> Date: 5/06/2009
  
> Time: 8:35:21
  
> User: N/A
  
> Computer: TEX41
  
> Description:
  
> Faulting application jetbrains.resharper.taskrunner.exe, version 4.5.1231.7, stamp 49dc9416, faulting module mscorwks.dll, version 2.0.50727.3053, stamp 4889dc18, debug? 0, fault address 0x0002d2b5.
> 
> For more information, see Help and Support Center at http://go.microsoft.com/fwlink/events.asp.

But that is not the only one, like the MS article says, I always had 1 other to go with that:

> Event Type: Error
  
> Event Source: .NET Runtime
  
> Event Category: None
  
> Event ID: 1023
  
> Date: 5/06/2009
  
> Time: 8:35:20
  
> User: N/A
  
> Computer: TEX41
  
> Description:
  
> .NET Runtime version 2.0.50727.3053 &#8211; Fatal Execution Engine Error (7A097706) (80131506)

<span class="MT_red">Coding is so much fun.</span>

BTW I really hate how they make you click at least 4 more times to get it to download. Why can&#8217;t they have a direct link?

Sorry for not taking pictures, I was a bit to frustrated to bother.

 [1]: http://ayende.com/Blog/archive/2008/12/17/kb957541-available-for-direct-download.aspx#feedback
 [2]: http://support.microsoft.com/kb/957541