---
title: 'Virtual Lab Tip: Notifications from Windows Event Log'
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-10-22T10:18:47+00:00
excerpt: There are a number of environments or situations where large-scale systems monitoring is either not cost effective or simply not available. Windows 7 and 2008 include a feature for sending notifications when specific events occur in the event log.
url: /index.php/sysadmins/os/windows/virtual-lab-tip-notifications-from-windo/
views:
  - 10323
rp4wp_auto_linked:
  - 1
categories:
  - 2008 Server
  - Windows
  - Windows 7
tags:
  - virtual lab
  - windows 2008

---
<div class="acc_header">
  There are a number of environments or situations where large-scale systems monitoring is either not cost effective or simply not available. Windows 7 and 2008 include a feature for sending notifications when specific events occur in the event log.<br /> <br /> <label>Technical Area:</label> Accidental Systems Administrator, Accidental Database Administrator<br /> <label class="diff">Level of Difficulty: </label><img src="http://tiernok.com/LTDBlog/dr_intermediate.png" alt="Intermediate Difficulty" /><br /> <label>Additional Articles:</label><a href="http://wiki.ltd.local/index.php/Virtual_Lab" title="View the wiki entry">Virtual Lab entry on the LTD Wiki</a>
</div>



## Using the Built-In Email

Windows Vista, 7, and 2008 have a built-in option that allows us to attach actions to events from the event log. These actions take the form of Tasks, similar to scheduled tasks with the exception that they are trigger by events rather than time. The services infrastructure in windows logs startup and shutdown events for services and many applications use the event log rather than a custom logging format. We can take advantage of these messages to create a notification that emails us when certain events occur.

First we need an example of the event and, being lazy, I&#8217;m going to generate one the easy way. Shutting down a SQL Server service generates the first event I want to track (and makes SQL admins everywhere cringe a little, mission accomplished). Later in the post we&#8217;ll cover creating tasks without restarting our server, since people tend to get upset when we do things like that in production.

When we open the Event Log from Administrative Tools menu in Windows, there are several logs available. SQL Server and other service log entries are logged to the Application log. To create my new event-driven task, I locate the entry I just created, right click it, and select &#8220;Attach Task to This Event&#8230;&#8221;.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/EventMonitor/orig/01_screen.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/EventMonitor/01_screen.png" alt="Attach Task to Event Dialog" /></a><br /> Attach Task to Event &#8211; Dialog
</div>

The Task creation option presents us with a fairly simple dialog. To be consistent and build good habits, we fill in a descriptive name and description for the task. 

<div class="hint">
  Filling in descriptive names and descriptions for items like tasks may seem like a waste of time, but it&#8217;s 20s of content now that will save a whole lot of digging around 4 years from now when you need to change it. Also, if I ever visit your shop I will make fun of you for not using a descriptive name 🙂
</div>

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/EventMonitor/orig/02_screen.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/EventMonitor/02_screen.png" alt="Names are Important" /></a><br /> Names are Important
</div>

The second step doesn&#8217;t require (or allow) an input and the third step presents us with the simple decision of sending an email, launching an executable, or displaying a message. We are here to send an email, so the choice should be obvious (but I still pressed the wrong one the second time through). 

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/EventMonitor/orig/03_screen.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/EventMonitor/03_screen.png" alt="Email Info" /></a><br /> Information for Email
</div>

After filling the email options out we continue to the last tab to review the task information before finishing. At this point we will want to check the &#8220;open properties&#8221; checkbox and press &#8220;Finish&#8221;.

After completing the wizard, we now have the full properties dialog for the task. Perhaps the most critical item here is to change the radio selection on the first tab to &#8220;Run whether user is logged in or not&#8221;. There is also an OS configuration box that I changed to Windows 2008 R2/Windows 7, but I am not sure whether this provides an addition features or simply satisfaction from making such an important decision.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/EventMonitor/orig/04_screen.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/EventMonitor/04_screen.png" alt="Change Summary - Finished" /></a><br /> Summary before Finishing
</div>

I have enough of the task filled in now that we can now press the OK button and try it out. We open Server Manager from the pinned icon on the taskbar (or via the search mechanism in the Start menu). On expanding &#8220;Configuration/Task Scheduler/Task Scheduler Libary&#8221; and selecting &#8220;Event Viewer Tasks&#8221; we should see the task we created. Right clicking the task and pressing &#8220;Run&#8221; will force the task to run once (again, just like testing a scheduled task). We can select the History tab to see the details of the run or wait patiently for an email to show up in our inbox.

<div class="screenshot">
  <a href="http://tiernok.com/LTDBlog/EventMonitor/orig/05_screen.png" title="View Fullsize" target="_blank"><img src="http://tiernok.com/LTDBlog/EventMonitor/05_screen.png" alt="Event Viewer Tasks view" /></a><br /> Event Viewer Tasks view
</div>

And there we have it, an email each time our service stops.

## Alignment of the Stars

If your mail server is like mine then it probably doesn&#8217;t allow anonymous relaying. If you are using Exchange and you are sending from the same account that you configured the task for (or the user is allowed to send for other users), then you may have received an email. Checking the alignment of the stars may also be of assistance. 

Even if the email did work, it is going to be rather tedious to create individual tasks for each of the potential events (SQL Server start/stop, SQL Agent Start/Stop, etc) that you are interested in.

Rather than build a hard-coded email task for each possible event, there is an option to run an executable. Tie that to Cscript.exe and a VBScript (or maybe a powershell script?) and you have a lot more flexibility to send emails. 

If you decide to go this route, you will probably want to edit your tasks to carry some additional information (like which event is firing). This will allow you to create a single script and task for multiple events:
  
http://blogs.technet.com/b/otto/archive/2007/11/09/find-the-event-that-triggered-your-task.aspx

## Not Perfect

This method of reporting from windows does not replace an external monitoring system. There is a wide range of errors that services and executables can generate. Covering them all would be extremely difficult even if we ignored errors that cause the box to lose communications or freeze entirely. However, if you have no budget at all, setting up some of these alerts will only cost you time and it will at least get you one step closer to knowing about problems before the phone rings.

<label>Additional Articles:</label>[Virtual Lab entry on the LTD Wiki][1]

 [1]: http://wiki.ltd.local/index.php/Virtual_Lab "View the wiki entry"