---
title: Keep your DBA guard up at all times
author: Ted Krueger (onpnt)
type: post
date: 2009-09-25T14:25:32+00:00
ID: 568
url: /index.php/datamgmt/dbprogramming/keep-your-dba-guard-up-at-all-times/
views:
  - 13709
rp4wp_auto_linked:
  - 1
categories:
  - Database Administration
  - Database Programming
  - Microsoft SQL Server
  - Microsoft SQL Server Admin

---
Crossing guards for schools are awesome! 

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//stop.gif" alt="" title="" width="367" height="420" />
</div>

As a parent of two boys, I entrust these people with stop signs in hand to ensure my kids (and all others) get across the busy intersections surrounding schools. 

I honestly feel comfortable knowing that large STOP sign will be throw up any time my children come to the intersection that leads them to school. 

A few weeks ago I had the pleasure of walking with my oldest son to school. I really enjoy these chances sense my commut is long and doesn't allow me them very often. It's any parent's enjoyment to see their children interacting with the other kids at an early age. It's amazing to see how they change personalities when they are mixed into other kids. Something about this day really bothered me though. On my way to pick my son up after school, I noticed the crossing guard sitting in her car, stop sign on the dash and parents running, skipping (one tumbling) across the busy road to school. What?!? The safety of the stop sign isn't good enough for the parents? No, I'm not that old but I feel this person should never let their guard down. It is their job to ensure that while school is in and within time frames that all pedestrians cross that road safely. If the parents cross unattended then when will it lead to kids maybe 13 to 14 years of age being ok to RUN FOR YOUR LIFE!

<div class="image_block">
  <img src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/blogs/DataMgmt//RUN.gif" alt="" title="" width="420" height="405" />
</div>

I had a very good conversation with the crossing guard. This lesson is in line with you as a DBA and handling what goes on with your database servers. Many times over, a DBA either becomes frustrated due to not getting security correct or is just lazy and grants far too much to a user or developer so they can get their tasks accomplished. Never, and I mean never let your guard down when it comes to this. 

## The Rules...

**_First rule:_** sa has no place beyond the DBA and even for the DBA should never be used for anything other than emergencies

**_Second rule:_** Secure to the lowest possible object. 

**_Third rule:_** Schemas and roles are there to protect you and your data. Use them!

**_Fourth and last rule:_** Do they really need that?

The first rule regarding SA should be a given to anyone. Even the accidental DBA that is thrown a few SQL Server instances to maintain simply for the reason things aren't working well on them. You have more than a problem with the abilities of SA and when it is used. You have no change management, no control over disaster and for most no accountability. This account should be thought of as the most classified object in your control. Cycle passwords weekly, apply DDL triggers to log usage, restrict, restrict and then restrict it further. If you come into a situation that SA was used by some fool with the ability to use a keyboard, do everything in your power and take as many all night workloads as it takes to get it cleaned up.

Second rule applies to simple deduction. If a user needs to read a table or even a set of tables in your database, do you give them datareader on the entire database? Oh how often that happens! Always bring yourself down as far as possible. Once again, schemas and roles can give you great power in managing that. The argument of this making your administrative job hard is utterly absurd and you should have gone into horticulture if that is how you think.

Schemas and roles are the database server god's gift to SQL Server. I cannot stress enough how easy a few strategic roles and schema setups can make a DBAs life easy in the long run. They are complicated! No, they are not. Just take the time to read a few articles and blogs on them. It will amaze you how well you can structure your databases and database servers.

Finally the question should always be raised is a requestor really needs what they think they need. Requests come in stating your SQL Development team needs a database set to TURSTWORTHY and they need ability to deploy and call up unsafe or external CLR procedures so they can work with files from the database. Let's think about that requirement. I love CLR and external events. It is the bread and butter of my administration. I can get things done that would take calling 300 external services and executables in previous versions. I'm the DBA! What happens when CLR is misused and not controlled? It can and will take you down. Would the request by the developer team be better suited outside SQL Server and then having read to the data into an executable which then does the file operations? Mostly, yes that is the better way to prevent unwanted problems in performance. That is not to say that CLR external operations are left completely out of development. Personally I believe the DBA should be the writer of those and allow EXEC to the developers. Control is yours so use it in a way it will keep you in your place and your database server healthy. 

Don't forget, saying no isn't all that bad for a DBA.