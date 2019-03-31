---
title: Please Mr. DBA, Change default passwords and use strong passwords
author: Ted Krueger (onpnt)
type: post
date: 2009-04-23T16:31:55+00:00
url: /index.php/datamgmt/dbadmin/please-mr-dba-change-default-passwords/
views:
  - 4079
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin

---
It&#8217;s your job to make sure security is not compromised. Part of that job is to make sure installations out of your control have not been left with default passwords set. If the passwords are controlled in the applications, then you have little control other than complaining until your blue in the face. If the accounts are SQL Authentication then they are in your control and you need to fix it.

Example: I can&#8217;t tell you what the system is of course but I can say the system holds highly sensitive data. The group managing the software installed it and got off running. A week later my network scan of instances picked up a SQL Express edition running on the server in which this software was installed. When I logged into the instance, BUILTINAdmins was enabled. SA at least had a password even knowing it took me 3 guesses to figure it out. What kind of got me in a bunch was a user I noticed set in the sysadmin server role. I am firm in believing this role has only one place and that is for either the DBAs SQL account or more securely handled, an AD group named DBA and so managed that way. The software didn&#8217;t think so though and the odd user was in the sysadmin role. I&#8217;m guessing some developer wasn&#8217;t smart enough to figure out any kind of security model for the software they were writing and instead of look like complete idiots by using SA, they created a user and gave them sysadmin. \*sigh\*

So why is this such a big problem? Here is how I cracked the password to this super user in under 10 seconds. Open google, type &#8220;default password {username}&#8221;, hit enter.

First hit I got back was another idiot posting in a forum begging for help. That doesn&#8217;t make them an idiot other than the fact they posted their DNS-Less connection string in the thread. When I tried the password they had in the string with my instance I was in and dropping objects. Well, I didn&#8217;t drop anything, but you get the idea.

So please, make sure you help out everyone by educating them how important it is to change default passwords, create solids security models and create strong passwords.

BTW..This is not made up. At some point in all of our careers it will happen. It&#8217;s almost as a sure thing as you either truncating your first table by accident or deleting something that probably was important 😉