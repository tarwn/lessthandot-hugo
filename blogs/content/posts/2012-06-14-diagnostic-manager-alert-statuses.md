---
title: Diagnostic Manager – Alert Statuses
author: Kevin Conan
type: post
date: 2012-06-14T13:22:00+00:00
ID: 1652
excerpt: "As you might imagine, I rely on Idera's Diagnostic Manager quite a bit.  Diagnostic Manager monitors many different things within each SQL Instance will alert to them when they met the thresholds you configure.  Now if you only a few SQL Instances monit&hellip;"
url: /index.php/datamgmt/dbadmin/diagnostic-manager-alert-statuses/
views:
  - 5904
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Microsoft SQL Server Admin
tags:
  - dba
  - diagnostic manager
  - idera
  - sql

---
As you might imagine, I rely on Idera's Diagnostic Manager quite a bit. Diagnostic Manager monitors many different things within each SQL Instance will alert to them when they met the thresholds you configure. Now if you only a few SQL Instances monitored, you may not have many alerts, but if you have dozens of SQL Instances monitored, you could have several dozen alerts all the time. 

Maybe some of those alerts are not going to cause your system to crash right away, but you want to keep them in mind to show your boss that you need more memory or disk space.

So how do you know if something critical just happened that you really should look at and isn't part of the other 50 alerts that just noise for right now?

This is where I use the different alert statuses and have defined what each status means.

Critical (Red) status means I want our support center who is watching the alerts page 24 ×7 to call me anytime of the day or night about it. I keep things like job failures, disk space that is really low and custom alerts about backups being really behind for example in this spot. When a alert goes to critical and it is not something that I need to know about immediately, I will re-adjust to only go as far as a warning level.

Warning (Yellow) status means that this is something that I want to know about and could be become a serious issue, but I don't need a 3AM wake up call about it. For me it's almost as important as a critical alert and is something that I will look at, but I won't drop everything right now for it.

OK (Blue) status means it's something I need to be aware of, but could go for days or weeks before I do something about it. For example, our dev system is using 90% of the available memory. That's fine because it won't be growing larger than that and we are actually migrating SQL Instances over to a new 2012 Environment.

This allows me to quickly sort through the alerts to see which ones I should be working on and concerned about (aka, prioritizing my time!)