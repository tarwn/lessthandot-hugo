---
title: Highly Available Presentations
author: Brian Davis
type: post
date: 2014-01-13T13:30:00+00:00
ID: 2227
url: /index.php/itprofessionals/professionaldevelopment/highly-available-presentations/
views:
  - 9522
rp4wp_auto_linked:
  - 1
categories:
  - Professional Development
tags:
  - Presentations
  - Presenting

---
A little over a month ago I posted the follow quick note on Facebook

[<img class="alignnone size-full wp-image-2228" alt="fb1" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/fb1.png" width="459" height="95" />][1]

The second comment I received was from Tom LaRock ([b][2] | [t][3]), who said...

[<img class="alignnone  wp-image-2229" alt="fb2" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/fb2.png" width="390" height="42" />][4]

My initial response was essentially "Nope, on my W530"

[<img class="alignnone  wp-image-2230" alt="fb3" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/fb3.png" width="390" height="39" />][5]

I answered quickly and probably should have elaborated, because Tom's next comment didn't surprise me at all.

[<img class="alignnone size-full wp-image-2231" alt="fb4" src="https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/fb4.png" width="478" height="50" />][6]

Essentially, what Tom is asking is "Is your presentation highly available?".  Tom makes a very good point and it's one that I recommend all presenters seriously think about!  It's a point that I thought about awhile ago.  Sadly I thought about it because no one had asked me a question similar to Tom's before and I hadn't thought about it on my own.

If you're presenting and your laptop fails or your VM's become corrupted or a drive fails or pick any one of a hundred other things that could go wrong just before or during a presentation, what would you do?  You might have anywhere from 10 to 200 people expecting you to present something to them and your laptop is dead in the water.  How do you deliver your presentation?  Would you be able to?

Have you built your presentation they way you would a production database that you depend on?  Did you include Disaster Recovery and High Availability in your presentation architecture they way you would in your database architecture?

What do I mean when I say "include DR & HA in your presentation architecture"?  I mean the same thing I mean when I ask if you've built DR & HA into your database architecture.  Can you recover your presentation from a disaster?  If you're hard drive crashes, can you recover your presentation to a point in time just prior to the crash?  Is your presentation highly available?  If something happens 15 minutes before your due to present, could you recover and still present on time?

#### Availability Groups, Clusters & Backups

I've been told in the past that the best case scenario is to have a secondary laptop all setup and ready to go.  Think of this being similar to availability groups or clustering for your database server.  You simply pull it out of your bag, fire it up and you're good to go.  Maybe this is a luxury you have and if so, that's great, but there are many that are unable to carry around a spare laptop, just as there are small companies that are unable to afford secondary servers for clusters.  And just like with availability groups or clustering, you could still have issues.  I've heard horror stories of primary and secondary laptops failing within hours or days of each other.  If you're out of the country or even out of town this could be a real problem.  If you don't have a spare laptop, what do you do?

What about backups?  Do you backup your presentations and associated files like you would your databases and encryption keys?  If not, you really should.  This is DR for your presentations.  If your presentation includes scripts and a database with staged data, do you have those backed up?  If so, then it's a good possibility that if your laptop crashed and someone said, "Here, use mine, I've got SQL Server installed.", that you could still give your presentation.

That's a great plan if your demos are fairly simple, but what happens if you're using a highly configured environment including multiple VM's?  Something like that isn't that easy to re-create...or is it?  Just like backing up your databases, consider backing up your VM's by copying them to an external drive.  Don't wait until the last minute either, backup your VM's as soon as possible after making any major changes.  (I learned this the hard way when a couple of my VM's were corrupted the morning of our last SQL Saturday in Cleveland)  If you've backed up your VM's the chances are pretty good that if your laptop crashes at a conference or even a user group that there will be at least one person with a laptop that can run your VM's.

So you've backed everything up, it's an hour before your presentation and your laptop crashes.  You try to recover and get your laptop running but it's not working and worse yet, no one has a laptop with the correct software to run your VM's...now what?  Are you stuck giving a presentation using screenshots of your demos?  It's better than nothing.  If only there was someone, something to save the day.  Wait, maybe there is...there's someone who is willing to let you use their laptop and there is an internet connection!  You're in luck, you can get to Azure!

#### Azure

Azure...did I really say Azure??  Yes I did.  Yet another tool in your High Availability Presentation strategy should be Azure.  Using Azure you can build your VM's in the cloud and be able to give your presentation, including demos, from almost any machine with an internet connection.  Not only that, but in Azure you can choose from VM templates that include SQL Server.  When I built my first SQL Server 2014 CTP2 VM in Azure, I was up and running in under 10 minutes.  Having the ability to do that also means that even if you weren't prepared and hadn't built your VM beforehand that it may be possible to fire up an Azure VM and use the scripts you backed up to create your database and still give your presentation.  I wouldn't recommend building a VM on the fly be part of your presentation HA/DR, but because of Azure's quick deployment, it's something to keep in your back pocket for times when all else fails.

Now you might be thinking "Azure sounds great. Why shouldn't I just build all my presentation demos in Azure and skip the rest?", but take a step back and consider the environments you might be or have presented in before you make that leap.  Don't get me wrong, Azure is great, but, in my opinion, it's not the be all end all for presentation demos.  I don't rely solely on Azure for my presentation demos because I've presented multiple places where there wasn't an internet connection or the connection was very slow or unreliable.  If you're presenting in a similar environment, you won't be able to rely solely on Azure for your presentation demos.

Building a highly available presentation is very similar to building a HA/DR database solution.  You need to have multiple options for availability and recovery to ensure success.  Don't put all your eggs in one basket!

#### Worst Case Scenario Planning (or Screenshots are better than URLT!)

It's always good to plan for the worst case scenario and with presentation demos that usually means that you are unable to run them despite your best efforts at being prepared.  Tom mentioned screenshots in his comment shown above and he's right.  Screenshots will be your best friend when all else fails.  With screenshots of your demos, you've got a visual as well as a talking point.  You may not be able to run your demos, but with the appropriate screenshots you can still show your audience what is going on instead of just talking about it.  Taking all the screenshots and highlighting different sections may be a tedious job, but if you ever need them, you'll be glad you did it.  My recommendation is to include the screenshots into your slide deck and then simply hide the slides.  If you ever need them all you have to do is un-hide the slides and they are already in the correct order.  Not only that but since they are part of your slide deck it means you don't have to flip back and forth between your slide deck and images which can make your presentation feel disconnected.

Remember, screenshots aren't meant to make your presentation amazing, they're meant to make your presentation available.  Having something for your audience to look at while you discuss it helps your audience understand and follow along.  If your demos are not working, having screenshots is definitely better than simply talking about them.

#### Building Your Own Highly Available Presentation Architecture

So far, I've presented a bunch of options and scenarios and you're probably wondering "What should I do to make my presentations highly Available?"  Well, that answer, like most with SQL Server is "It Depends".  It depends on what you have available to you, what your environment is and what your presentation entails.  Ideally, I try to follow the basic steps outlined below when building and giving a presentation.

##### Building a Presentation

  * Create slide deck stored on SkyDrive (or DropBox, GDrive, etc)
  * Create solutions/projects/scripts stored on SkyDrive
  * Build any necessary VM's locally and regularly back them up to an external drive separate from where they run
  * When the demos get to the CTP/RC stage 
      * Start implementing them in Azure
      * If you've got a secondary laptop, now is the time to setup that environment as well
      * Take screenshots of demos

##### Before Presenting

  * Copy slide deck, projects, scripts, database backup, etc to an external USB drive
  * Ensure all VM's are backed up to an external USB drive
  * If you have a secondary laptop make sure it contains the latest version of your presentation and demos
  * If you are using Azure make sure it contains the latest version of your presentation and demos
  * Create a zip of any materials (PDF of slide deck, scripts, backups, etc) that are to be distributed and copy it to an external USB drive as well as SkyDrive

All these steps may not be necessary for every presentation you have, however they should help you start thinking about the steps that will be necessary to make your presentations highly available.  Don't let an unexpected failure or problem ruin your next presentation.  Take a little extra time to prepare before hand and you'll be able to recover from almost any disaster that may arise during your presentations.

#### Feedback

In this post I've written about the steps that have worked for me and hopefully made you think about how you can make your own presentations highly available.  I'd love to hear about the steps you've taken for your own presentations!  If you've got other steps or ideas that work for you, please share them in the comments below.

 [1]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/fb1.png
 [2]: http://thomaslarock.com/
 [3]: http://twitter.com/SQLRockstar
 [4]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/fb2.png
 [5]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/fb3.png
 [6]: https://lessthandot.z19.web.core.windows.net/wp-content/uploads/2014/01/fb4.png