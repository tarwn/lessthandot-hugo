---
title: LessThanDot Outage – Aug 22, 2013 to Sept 4, 2013
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-09-04T20:38:00+00:00
ID: 2149
excerpt: "Over the past couple weeks you may have noticed that LessThanDot was completely down. I know we did. We're working on solutions to our missing DR plan and shoring up some holes we found in our infrastructure (we had backups, they were just a little, er,&hellip;"
url: /index.php/itprofessionals/itservicemanagement/lessthandot-outage-2013/
views:
  - 10218
rp4wp_auto_linked:
  - 1
categories:
  - IT Service Management
  - Other
  - Running My Own IT Business
tags:
  - disaster recovery
  - outage

---
Over the past couple weeks you may have noticed that LessThanDot was completely down. I know we did. We're working on solutions to our missing DR plan and shoring up some holes we found in our infrastructure (we had backups, they were just a little, er, stale).

Cue jokes about how a group with professional developers, architects, project managers, managers, consultants, etc didn't have a DR plan.

Rest assured, this site means a lot to us (the founders), us (the people who blog here), and us (the people who get information or look up resources here). Along with updates to the “holy crap where did the site go” plan, this may be the jab we need to get back to some other feature-y changes we had lost steam on.

During the outage, I kept a temporary Azure website up with some information about the outage and a link to the monitoring (that I finally implemented an hour into the outage, oh well). We'll have additional details and reactions posted in the blogs about the outage in the near future, but here is the content of that post for those that had not yet had a chance to see it:

* * *

Thursday evening (my time), twitter messages started flowing in from LTD'ers who couldn't access the site. 

<div style="text-align:  center; color:  #666666;">
  <img src="http://lessthandot.azurewebsites.net/images/tweets.png" alt="Twitter: Who ate our server?" title="Twitter: Who ate our server?" style="max-width: 600px;" /><br /> Indeed.
</div>

## DDOS?

Our host, [host4geek][1] responded 4-5 hours into the incident to let us know a DDOS was occurring against the facility our server was in (which would not be covered by the [SLA][2]). I added the site monitoring I had always intended to add but
          
never got around to. That way I'd get an alert when it came back up (or a measurement if it did not). 

<div style="text-align:  center; color:  #666666;">
  <a href="http://stats.pingdom.com/0nt3y09cs5iy/935183"><img src="https://share.pingdom.com/banners/4931d952" alt="Uptime Report for http://ltd.local: Last 30 days" title="Uptime Report for http://ltd.local: Last 30 days" width="300" height="165" style="max-width:  300px" /></a><br /> Click image for the full status page
</div>

Thank you [pingdom][3] for a slick and easy setup process. The stats will look funky since I set this up an hour or two into the outage, so there's no successful uptime recorded earlier in the month.

## Where's the Server?

Late Friday afternoon we received an update from Host4Geeks. Apparently they have a provider for the facitility and that provider had been planning a facility move. The provider had scheduled server migrations and then apparently Thursday night decided to just load up the remaining servers on a truck and take them to a third facility. or something. From
          
what I can tell this doesn't fall under natural disasters, DDOS attacks, or planned maintenance, so we'll also be working on getting credits for the service outage. 

## So…ETA?

So where is our server? I'm not entirely sure. We have not been given an ETA on when we can expect to be back online. Obviously this sucks for you, because you were probably trying to get to some very useful content. It sucks for us because we'd like to write some more really useful content. In the meantime, we'll be working on some more content for everyone, considering our hosting options, and we'll update this page as we have more information. 

## Updated, Server Still MIA, 2013-08-27 9:45PM EDT (28th 1:45AM UTC)

Good news, our host has informed us they recovered some of their servers! Well, not our server, but woohoo! Er…yeah. We have now been down for over 120 hours. We will be back, it's just a question of whether they find our server before they have to offer us free hosting for life or not. We've also been discussing better disaster recovery plans (it's a good thing none of us do that for a living, oh wait…), so on a positive note once we get through this outage we'll be in a better place. And how often can you tell people your server is down because it was migrated to a truck in a dark parking lot somewhere… 

## Updated, No SLA, No ETA, 2013-09-03 {#updated}

After prodding them again, our &#8216;host' has admitted they have no ETA on getting the remaining servers (ours included). We have also noticed that dedicated servers are specifically excluded from the SLA, so in essence the policy of our host is “downtime? sucks to be you”. We also found out that we forgot to finish setting up backups when we set up the server with them, so our most recent backup is March. They have told us our options are to setup a new server from our backups or “wait indefinitely” for our server to be located and brought online. 

We will likely have to go the new server setup route, the next question we will have to consider is whether to do it with this host (who is paid through the end of the year), or find another and raise funds early for another year. 

* * *

Luckily as we were building out the new server, the old one finally came back online. More to come soon (including back to our regularly scheduled outpouring of technical blog goodness).

 [1]: http://host4geeks.com
 [2]: https://host4geeks.com/tos/
 [3]: http://pingdom.com