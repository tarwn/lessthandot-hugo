---
title: Using Windows Server 2008 as a Desktop OS
author: Alex Ullrich
type: post
date: 2009-05-07T12:17:00+00:00
url: /index.php/sysadmins/os/using-windows-server-2008-as-a-desktop-o/
views:
  - 3498
rp4wp_auto_linked:
  - 1
categories:
  - 2008 Server
  - Operating Systems
  - Windows

---
I recently built a new PC, and I needed to get a 64 bit operating system. I had a copy of Server 2008 sitting around from MSDN, so I thought why not use that? If I did, I could run Hyper-V and some of the other cool server gadgets, and the tradeoff in user experience would be minimal. With some guidance from a friend I soon realized that I could do this with no sacrifice in the user experience at all.

The first thing to do is install the Windows Experience feature from Server Manager. This was pretty painless, but it did require a restart. This gives you things like &#8220;Windows Media Player, desktop themes, and photo management&#8221; according to the description. It also gives you Windows Defender.

Once this is done, you need to decide whether you want to run Aero or not. I rather like the look of it, so I decided in favor. To enable this you need to open services.msc and go to the properties for the &#8220;themes&#8221; service. Set the startup type to &#8220;Automatic&#8221;, start the service, and hit OK/Apply to save. You can then go to Personalize -> Window Color and Appearance and the Aero color scheme will be available. 

Next is turn off the enhanced security in Internet Explorer. Again, pretty simple. From server manager, choose &#8220;Configure IE ESC&#8221;. In my case I had to turn it off for both Administrators and Users. 

Finally, the last thing I did was change the password policy. To do this start up secpol.msc and look under Account Policies->Password Policy. I set the Maximum password age to 0 so that passwords would not expire. Then disabled the &#8220;Password must meet complexity requirements&#8221; option.

That was really all there was to it, I was kind of surprised it was so easy. But now I have a computer with all the server goodies that I would like to play with that my girlfriend will not want to kill me every time she uses. Well she might want to kill me but at least not because of how I set up the computer 😀