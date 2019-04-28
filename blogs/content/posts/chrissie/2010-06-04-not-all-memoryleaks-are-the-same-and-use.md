---
title: Not all memoryleaks are the same and USER objects.
author: Christiaan Baes (chrissie1)
type: post
date: 2010-06-04T06:27:42+00:00
ID: 808
url: /index.php/desktopdev/mstech/not-all-memoryleaks-are-the-same-and-use/
views:
  - 5058
categories:
  - 'C#'
  - Microsoft Technologies
  - VB.NET
tags:
  - 'c#'
  - memory leak
  - vb.net

---
I have an application that is used on a bigscreen TV to show some stats and calender things. The application runs 24/7. It is not something that I want to spend lots of time on.

After I made some changes to the application it started crashing every 12 hours or so. The time it took to crash seemed pretty consistent too. Just restarting the app would work but gets annoying after the second time. It wasn&#8217;t actually the app as a whole that crashed either. Just a few richtextboxes that change every 10 seconds that stopped updating. They were stuck.

Looking at the log file I saw an error coming from the timers that did the updates. 

> Error creating window handle

That was very nice.

So I started the application, openend Windows task manager and added the columns threads and handles to the list. And waited for it to crash or at least see something change.

> <span class="MT_red">FYI: an application will crash once it reaches 10000 handles.</p> 
> 
> <p>
>   Edit: Apparently this information is not correct since I just noticed an app having 81000 handles on the picture below.
> </p>
> 
> <p>
>   Edit2: the limit is 10000 according to <a href="http://support.microsoft.com/kb/327699">this MS article</a> but there is a hotfix for it.</span>
> </p></blockquote> 
> 
> <p>
>   But the handles count didn&#8217;t go up, it fluctuated around the 400 mark and the Mem Usage also didn&#8217;t cause any problem I could see.
> </p>
> 
> <p>
>   So I went to google and found <a href="http://stackoverflow.com/questions/222649/winforms-issue-error-creating-window-handle">a question on StackOverflow</a>.
> </p>
> 
> <p>
>   And one of the answers by user AlfredBr said this.
> </p>
> 
> <blockquote>
>   <p>
>     This problem is almost always related to the GDI Object count, User Object count or Handle count and usually not because of an out-of-memory condition on your machine.
>   </p>
>   
>   <p>
>     When I am tracking one of these bugs, I open ProcessExplorer and watch these columns: Handles, Threads, GDI Objects, USER Objects, Private Bytes, Virtual Size and Working Set.
>   </p>
>   
>   <p>
>     (In my experience, the problem is usually an object leak due to an event handler holding the object and preventing it from being disposed.)
>   </p>
> </blockquote>
> 
> <p>
>   So I added the columns User Objects and GDI objects to the Windows task manager. And waited some more. It became clear very soon, that User objects count was growing by 1 every 10 seconds.
> </p>
> 
> <p>
>   This is after 10 hours of running.
> </p>
> 
> <div class="image_block">
>   <img src="/wp-content/uploads/blogs/DesktopDev/Userobjects1.png" alt="" title="" width="589" height="424" />
> </div>
> 
> <p>
>   And you have to trust me on this but the crash happens when it reaches 10000.
> </p>
> 
> <p>
>   The reason why the user objects count was growing was because I was doing this every 10 seconds.
> </p>
> 
> <pre lang="vbnet">
private RichTextBox Text;
public string MakeRtf()
{
  Text = new RichTextBox
  {
    SelectionAlignment = HorizontalAlignment.Center
  };
  Text....;
  var returnvalue = Text.Rtf;
  Text.Dispose();
  return returnvalue;
}</pre>
> 
> <p>
>   Yes I am using a RichTextBox control to make RichText in a class and then use that to copy it into another RichTextBox. Apparently the Dispose is not anough to release the USER object count.
> </p>
> 
> <p>
>   So I changed the above code to this.
> </p>
> 
> <pre lang="vbnet">
private RichTextBox Text = new RichTextBox
  {
    SelectionAlignment = HorizontalAlignment.Center
  };

public string MakeRtf()
{
  Text.Text = "";
  Text....;
  var returnvalue = Text.Rtf;
  return returnvalue;
}</pre>
> 
> <p>
>   And that made a big difference since USER object count now sticks at 80.
> </p>
> 
> <div class="image_block">
>   <img src="/wp-content/uploads/blogs/DesktopDev/Userobject2.png" alt="" title="" width="727" height="459" />
> </div>