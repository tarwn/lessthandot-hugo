---
title: Backups are for sissies!!!
author: Ted Krueger (onpnt)
type: post
date: 2009-03-11T11:49:22+00:00
ID: 345
url: /index.php/datamgmt/dbadmin/mssqlserveradmin/backups-are-for-sissies/
views:
  - 7151
rp4wp_auto_linked:
  - 1
categories:
  - Microsoft SQL Server Admin

---
For quite a while, now my signature and IM status message has stated, “Backups are for sissies!!!”. Recently I had a coworker laugh about this when they saw it on Google chat. They thought it was hilarious. I couldn't help but stop them and explain how the statement really wasn't a joke and in reality was probably the most serious statement I've ever branded to my online handle. The true meaning goes deep into disaster and recovery of the systems I manage and put my reputation on the line daily by reaching uptime levels that surpass expectations.

> <span class="MT_orange">Note: Everything below is NOT an excuse not to perform backups. That isn't the point! The point is: never ONLY perform backups and think you are completely safe from all types of disasters or normal data requirements 🙂</span>

One of the largest mistakes IT groups make with managing data is only relying on backups. There is this mentality that people have where if they have a backup, they can recover from a disaster no matter what the disaster is. Now we all know a disaster can mean many things. It is not localized to complete failure of hardware. It can be as small as one table truncation event. You will be judged on your ability to recover, but also at the speed in which you recover, so your business processes can continue. Yes, you have to discuss with the business what exactly a disaster to them is. It's your job to compile that information into a technical process to prevent the business from losing money.

So what can you do? Database servers in general give you plenty of options. Focusing on SQL Server you have mirroring, log shipping, replication, DB copy methods, SSIS and a few others that could go in there. Here come the complaints! Those native SQL Server processes are far too complex for us to setup without a fulltime DBA. Let me apologize to all the DBA's out there that convinced their managers that they are the smartest people on earth because they could configure a mirror before I say, “No, it really is that easy”. With SQL Server 2005+ there was a big push for wizards in the prepackaged management tools. Given SSMS there isn't many things you'll find that you can't accomplish with a wizard. I found this out when taking the certifications for SQL Server 2005. I had not been prepared for the amount of GUI related questions the tests contained. Being a DBA and Developer for many years that have always been prone to code things, I actually stumbled a bit but made my way through it. Now when I need quick tasks done the wizards are actually friends to me. Why take 10 minutes to write it if I can click 3 buttons in 1 minute to get to the same result? This all goes back to being a lazy DBA, but probably a better employee by means of cost savings in time.

I know everyone's level of which the term “Easy” is used can be different but it really is that easy to setup mirroring, replication and the rest with SQL Server. It may not be easy to maintain and will take effort to monitor these processes. There will be the requirement for you to think and to gain some knowledge of the systems. But isn't that why you went into information technology? Didn't you get into it to learn technology and enjoy it as we go to the next phase? Still amazes me, the vast number of people in IT that hate learning new things or simply lay back and pretend to be happy with their null career. The only thing I have to say to that is quit. Please let someone motivated fill the void you create in the group. Star bucks may be hiring if you're lucky. Then you only need worry about learning how to make this month's new coffee blend.

So backups are for sissies means much more than you may have thought. The basis of the statement is to portray the facts that if you rely on backups alone you're only delaying a slow death by means of a disaster. 

Following this morning's rambling session, I plan to create three blogs based around the top 3 most commonly used DR methods. I use them all for various reasons and for 3 points of recovery. Those methods will include mirroring, log shipping and replication. I'm going to do these completely by means of SSMS and no coding to show you simply how simplistic the basic setup and configuration of these native SQL Server methods can be. I will also show you the tools that are packaged in SQL Server to help you monitor them. Yes, they give you tools to monitor them and they are part of the bundle. Further down the path there will be the need to discuss hardware and OS level configurations. That goes into Disk, CPU configurations, Memory and on. This is where complexity comes in but if these are not pointed out and typical configuration options mentioned you can actually hurt yourself be doing things like mirroring.