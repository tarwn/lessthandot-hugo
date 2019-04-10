---
title: 'TSQL Tuesday #32 – A wicked fun day.'
author: joshuafennessy
type: post
date: 2012-07-18T00:09:00+00:00
ID: 1675
excerpt: "Erin Stellato (blog | twitter) announced the most recent incaration of TSQL Tuesday a couple of weeks ago.  A Day in the Life.  A very interesting topic indeed.  I knew I wanted to participate, but I didn't want it to be a normal day, as those are litte&hellip;"
url: /index.php/itprofessionals/itprocesses/tsql-tuesday-a-wicked-fun/
views:
  - 5428
rp4wp_auto_linked:
  - 1
categories:
  - IT Processes
  - Professional Development

---
Erin Stellato ([blog][1] | [twitter][2]) announced the most recent incaration of TSQL Tuesday a couple of weeks ago.  <a title="TSQL Tuesday: A Day in the Life" href="http://erinstellato.com/2012/07/invitation-for-tsql-tuesday-day-life/" target="_blank">A Day in the Life</a>.  A very interesting topic indeed.  I knew I wanted to participate, but I didn't want it to be a normal day, as those are littered with meals, work, and answering a billion questions from a 6 year old mind..

I knew I had a trip to Boston coming up, so I thought, hey, I should chronicle a day on the road.

I'm going to actually follow a proper 24 hour day, starting at 9pm on Monday 7/16, finishing on 9pm Tuesday 7/17. Here goes…

**Monday** 

**9:00 pm** — I'm in Baltimore at the airport.  THERE ARE CHARGING STATIONS EVERYWHERE.

**9:15 pm** — Wrapped up a call with a coworker sharing a Cognos migration story

**9:30 pm** — Time to leave.  Where's the plane?

**9:55 pm** — Oh, there it is.

**10:15 pm** — Airborne.

**11:52 pm** — Deplaning at Logan

**Tuesday**

**12:05 am** — Got my bags

**12:30 am** — Hey look, there are only minivans left.  Sweet.  Damn this thing is comfortable.  It feels wrong, but oh so right.

**1:15 am** — Nice room.  Got a suite with a kitchen so I can cook instead of going out.

**1:45 am** — Log in to work.  Index rebuild finished.  Check various other things, enter my time for the day.

**2:30 am** — Sleep

**7:30 am** — I'm up.  Not happy.

**8:30 am** — Ok, I'm fed and on my way to my client.

**8:40 am** — Wait, wasn't that my exit.

**9:00 am** — Finally here.

**9:05 am** — 5th floor.  Hand the IT manager a 'bag of Kerberos'.  Inside joke.  Laughter ensues

**9:15 am** — Logged in, whiteboarding already.  Working through execution paths for ETL processes.  Making    
sure that timing of all data files are scheduled correctly.

**9:25 am** — Discussed some new data irregularities.  Worked through solution on whiteboard.  Minor changes    
will be required, mostly to a couple of queries.  SSIS work _probably_ won't be needed.

**9:45 am** —  Showed off some of the ETL and auditing reports I've implemented.  Discussed a few new    
features that we would like to add.

**10:30 am** — Reviewing latest data load.  Something seems off with a few of the fields.  A handful of them are    
correct, but the data is right in the final 3 fields. This is sourced from COBOL fixed width files, so    
either my code that parses the file is off  a bit, the mapping document is off.  Working out which    
one it is.

**11:30 am** — Manually parsing through fixed width COBOL output is hard.

**11:45 am** — It's my code.  Correct quickly made and deployed to DEV.

**12:00 pm** — Code executed.  Looks much better.

**12:30 pm** — Lunch.  Roast Beef panini.  Yum.

**12:45 pm** — Everyone is making jokes about Twitter.  I show them #sqlhelp.  3 of the 4 are now on Twitter.

**1:00 pm** — Back for some new ETL development that we discovered.  I'm mentoring this client a bit, so we         
did some of the work together.  Really enjoyed watching his face when “it clicked”.

**2:00 pm** — Working through some questions on the data warehouse load.  Originally we were going to load    
by date, but it appears that we are going to have to load by date and SystemCode.  A wrinkle, but    
nothing too major.

**3:00 pm** — After finally having enough of dealing with poor performance of the dev server, decided with    
client's support to set memory limits on SQL.  6GB should be enough for now.  At least we'll have    
some free RAM so the server isn't paging constantly.  Things seem a bit better.  Server is still    
WAY to small to be working with a dataset of this size, but at least I can open SSMS now.

**3:30 pm** — Meeting to discuss project status, as well as some other things on a list they have going.  The IT    
manager is extremely interested in Kerberos as he is getting lots of conflicting stories internally    
about how it works.  I tell him about DelegConfig.  He is ecstatic.  Interal resources mentioned    
that there would be now way of testing the infrastructure after configuration. I think he's going    
to keep it in his back pocket and use it when the time is right.

**4:00 pm** —  Hmmm, test job failed….this isn't good.

**4:10 pm** —  Appears that tempdb is suffering some issues with an ACTIVE_TRANSACTION status in the    
log\_reuse\_wait_description column.  To the boogle!

**4:45pm** — Still nothing really, confused.  Client finds Robert Davis' ( [twitter][3] | [blog][4] ) blog while searching for    
info.  Makes joke about SQL Cruise.  I explain that my friend Tim Ford ([twitter][5] | [blog][6]) runs it.     
I explain what it is.  He sends email to manager asking about training funds…

**5:00 pm** — Figure out (with client's help) how to get to Fenway from their office.  Going to a game    
tomorrow night.

**5:30 pm** — Wrap up at the office, still no solution to tempdb issue.

**6:30 pm** — Stopped at Target for groceries for the week.  Back at the hotel now.  Logging in to kick off job    
again and see if it “just works”.  It doesn't.  Making list of changes made today so we can back    
the out one-by-one tomorrow while the DBA monitors the situation.

**7:00 pm** — Running gear on.  Heading to <a title="Pond Meadow Park" href="http://pondmeadowpark.org/" target="_blank">Pond Meadow Park</a> for a run.  Client warned it was hilly.

**7:30 pm** — At the park.  HOLY BALLS THIS IS A FRICKEN MOUNTAIN CLIMB.

**8:02 pm** — KICKED THAT TRAIL STRAIGHT IN THE JUNK FOR NEARLY 3 MILES.  Seriously.  I feel so    
strong right now.  I can do anything, so I don't know, call me maybe.

**9:00 pm** — Stopped back at Target to get a couple of things that I forgot the first time.  Made a wrong turn    
to get back to the hotel, ended up on the expressway again.  Turned back around.  Finally back    
to “home”.  My brain never seems to work right after a good run.  Takes an hour for it to reset.

**9:30 pm** — Salmon filets cooked.  Rice and broccoli as well.  Begin writing blog post for TSQL Tuesday.

**10:12 pm** — HOLY CRAP IT'S NOW

**10:20 pm** — I THINK I BROKE THE TIME-SPACE CONTINUIUM.

**10:45 pm** — Future Josh is logging back into work.  Going to get a head start on some changes discussed today for    
tomorrow. I have a feeling tomorrow is going to be full of figuring out the tempdb issue…

#CONNECTIONTERMINATED

 [1]: http://erinstellato.com/
 [2]: http://twitter.com/erinstellato
 [3]: http://www.twitter.com/SQLSoldier/ "Robert Davis on Twitter"
 [4]: http://www.sqlsoldier.com "SQLSoldier dot com"
 [5]: http://www.twitter.com/SQLAgentMan/ "Tim Ford on Twitter"
 [6]: http://www.thesqlagentman.com "The SQLAgentMan dot com"