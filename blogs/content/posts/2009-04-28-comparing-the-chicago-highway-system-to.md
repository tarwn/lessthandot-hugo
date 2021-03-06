---
title: Comparing the Chicago Highway System to your Disaster/Recovery Plans
author: Ted Krueger (onpnt)
type: post
date: 2009-04-28T11:24:51+00:00
ID: 401
url: /index.php/datamgmt/datadesign/comparing-the-chicago-highway-system-to/
views:
  - 9849
rp4wp_auto_linked:
  - 1
categories:
  - Data Modelling and Design

---
The drive to work today was a short 2 hours over the normal 40 minute commute. It turns out there was a semi that drove off a bridge and landed face first on the train tracks below. This caused several problems with traffic this morning. Both north and south bound lanes of the interstate were at a crawl and to top it off, the major railway that brings commuters to and from the greater Chicago area was halted due to the semi kind of being in the way. Of course EMT and crews to clean the situation up were quick to the scene, but the task of this major cleanup would take hours. In the meantime the traffic situation in what is already known as the worst traffic area in America is at a stop. My first concern was the semi driver. I commute to the northern part of Wisconsin very often and when you drive the highways in the northern areas of Wisconsin, you start to get respect for what these people do. Then I started thinking about the situation as one large entity and quickly came to realize it was so similar to a poorly planned disaster/recovery plan that I thought it deserved this posting. 

So let's think about the semi and traffic problem in the great state of Illinois this morning. The cause was the semi derailing, if you will, from the highway. This caused the cascading events of the railway systems to stop, highway systems to stop and then even as far as the minor county highways and smaller city roads to become backed up. Now let's think about your database server in this situation. First event is your database server derails and becomes unavailable. This causes the event chain very similar to the traffic issues from the semi event. First the critical business applications stop or data starts to backup at the client level. This then spreads to the smaller applications that require the critical systems integration of data to slow and finally crawl and become unusable. The situation as it stands is you have nothing more than backups to save your existence and regain anything of a database server that is left. What happens now is it will take you hours to repair the situation just as the cleanup will take hours for the semi and traffic issues this morning. Basically now your business is the traffic jam in the greater Chicago area this morning. Pretty much your business is at a standstill.

Here is the depressing part. Yes, the business is losing millions of dollars, but this is not the first thought on your mind. Instead the first thought is the health of the semi driver. This goes with the health of your job. Basically it isn't going to be so healthy and you may need major surgery to repair the damage from falling face first going 65mph off a bridge. 

First let's fix the traffic problems that came from the semi first taking out north and south bound lanes. We fix this by sending millions of our bailout funds (HA!) into the construction of a secondary underground highway system. This underground system is a dust collector and known as the sleeping giant waiting the time when it becomes the commuter's hero in arms. Installations of barriers and onsite crews go in as to direct traffic safely into the secondary highway system once the save me button is pushed. Next we need to think about the railway systems. This one is easier. In an orderly manner we have the ability to convert the rail car systems into passenger trains. Directly thousands of commuters to these other rail systems, we prevent thousands from being hours behind schedule over minutes.

You want to know the funny part of this fictional story of a failover commuter system? It would cost millions if not billions to accomplish. But to fix this in comparison to the same situation happening to your database landscape and configuration would cost pennies comparable. Short story is, we fix the north and south bound with mirroring. We then fix the major railway system with log shipping offsite and redirection of the connectivity of the minor business systems that greatly rely on the accuracy of the major systems. Again, minutes to failover versus hours of repair to bring it back to life. 

So my long winded meaning of this useful bit of information is, you really do need a disaster/recovery plan in place. Disasters do happen and the last place you want to learn this is in the event of one. First thing, you don't want to be the semi driver. Second thing is you don't want to cost the commuters of the world millions due to your fumbling idiocy of not setting up something as simplistic in comparison to an underground standby highway and railway system 😉