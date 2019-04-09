---
title: Hiding Outages is a Short Term Game
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-10-29T17:32:00+00:00
ID: 1772
excerpt: "5:20 PM on Saturday night, looking for the correct size air filter at the local superstore. My phone dings and chirps, as I receive almost simultaneous email and twitter alerts; our production application can't talk to it's database. As a SaaS company, it rolls downhill. An unexpected outage with our application means an unexpected service outage for our customers. Time to head home and start troubleshooting."
url: /index.php/itprofessionals/itservicemanagement/hiding-outages-is-a-short/
views:
  - 14011
rp4wp_auto_linked:
  - 1
categories:
  - IT Service Management
  - Running My Own IT Business
tags:
  - communication
  - outages

---
**5:20 PM** on Saturday night, looking for the correct size air filter at the local superstore. My phone dings and chirps, as I receive almost simultaneous email and twitter alerts; our production application can't talk to it's database. As a SaaS company, it rolls downhill. An unexpected outage with our application means an unexpected service outage for our customers. Time to head home and start troubleshooting.

**5:45 PM**. After logging into the service provider dashboard, I find that the database services tab is completely non-functional. Attempts to connect to the database from home also fails. Various other troubleshooting directions are followed and also meet in failure.

And then some scheduled jobs in our development and staging environments kick off and also report database timeouts. It's getting increasingly unlikely that this is us, time to contact support.

**6.45 PM**. Calling support leaves me in a hold queue that is experiencing “longer than normal call volume”. Maybe this isn't just me? I check the service status page and everything is green. Still waiting on hold. I check twitter and don't see any mentions. 

**7:10 PM**. Lets check the public forums…ah, there's a few other people with this problem. I add my own personal story so we can help flesh out the picture of what's going on. 

**7:15 PM**. 30 minutes into the hold queue and I just got transferred to a ringing phone. After seeing the forum post, I suspect I'll end up getting a canned “We know there's a problem, we don't know what it is or when it will be fixed, thank you for all your money” speech. So I'm a little surprised when after two minutes of ringing the phone simply goes dead. 

By now I've added a few tweets to twitter with the appropriate tags, started looking at the SLA information for the service, and generally just continue to waste my evening in front of the computer instead of spending time with my wife and two year old. 

And time passes.

**8:10 PM**. I've been refreshing the status dashboard every 10 minutes since I first opened it but this is the first change. Red notifications show up, along with hourly messages going back in time to 5:11 PM. I add a note to the forum and a tweet for anyone else waiting for some word from our service provider. The messages are fairly generic and innocuous, admitting that some customers may experience outages with given services and regions. We apologize for the inconvenience.

We couldn't be bothered to provide an update for 3 hours, but we apologize for the inconvenience.

Your business has been down for three hours and there was no official announcement you could point your customers to, but we apologize for the inconvenience.

## Admit Your Service Outages?

I would bet we have all worked with companies or products that go to great pains not to announce bugs or outages to their end users. After all, admitting an outage means that the people that didn't get affected will know about it too, and that looks bad.

Maybe we're afraid of upsetting users that weren't online at the time, of losing customers, of warning off potential users, of providing inside information to our competitors, or getting negative press.

### Been There…

I once worked with a manager that screened each outage email, broadcasting only the ones that he felt were important enough to be forwarded, which tended to be none of them. Our service levels improved amazingly in the short term. Unfortunately since we weren't keeping our users informed, each outage took longer to correct as we also fielded calls from end users thinking it was just a problem with their system. Which meant our mean time to fix outages went down and affected more people. Which meant that the overall public consensus of our service level got poorer, especially as people realized we were simply not reporting our outages anymore. 

We lost credibility. Our service levels were reduced to levels we had worked years to raise them from. The trust our end users extended to us was heavily impacted. 

Before that slow slide back into the mud, we had originally climbed out by being open about our outages. Every outage had been announced, often before we had a fix identified, with follow-ups when information was available. Users trusted us more because they could see us jumping on problems, hear us being honest and open with them, and see the problems getting taken care of. We didn't have to tell them how much things were improving, they saw it. 

But we stopped admitting our outages, and for a short time we looked even better. Then we looked far, far worse.

### Pretending Doesn't Make It So

Hiding bad news to protect your image is a short term loan with major long term costs. Your statistics are broken, so justifying and performing root cause analysis is that much harder. You are limited in what you can ask or discuss with your customers, because each one has to be treated as a potential leak. Your support services are put under a lot of strain and probably built with a higher focus on cost reduction than quality of service.

In short, all of the energy you are spending to look awesome is actually moving you further away and unlikely to be fooling your end users.

## Back to Saturday Night

Saturday night, a well known service provider had an outage that shut down our business for 3 hours and 31 minutes. 

For 3 hours we were in the dark and then, when an update was provided, it was backdated to look like we had been kept in the loop the whole time. Countless hours were wasted as we and many other customers tried troubleshooting a problem that wasn't in our systems. The support center was overloaded (or just not responding). And at the end of the night a degree of credibility and trust was thrown away.

Someone had an opportunity to shine, and instead chose to make a bad situation that much worse.