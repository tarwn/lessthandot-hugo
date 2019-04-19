---
title: Process Kills Developer Passion...and Kittens, Lots of Kittens
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-05-17T15:34:00+00:00
ID: 1177
excerpt: 'So recently I found out that process kills developer passion. Actually I found out over and over, as numerous people sent me the link. In fact, I even heard certain past co-workers silently wishing they could send me the link (well, they would if they h&hellip;'
url: /index.php/itprofessionals/itprocesses/process-kills-developer-passion-and-kittens/
views:
  - 12777
rp4wp_auto_linked:
  - 1
categories:
  - IT Processes
tags:
  - best practice
  - kittens
  - lots of kittens
  - pdca
  - rup
  - scrum
  - waterfall

---
So recently I found out that [process kills developer passion][1]. Actually I found out over and over, as numerous people sent me the link. In fact, I even heard certain past co-workers silently wishing they could send me the link (well, they would if they had read it, anyway).

Except I didn't really learn about processes killing developer passion, what I learned was that best practices take a lot of time away from programming and, what's more, are generally followed poorly. And process kills developer passion.

<div style="border: 1px solid #AAAAAA; float: right; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/trent/2003_12_13_14.gif" alt="Kitten" style="padding-bottom: 5px; max-width: 250px;" />
</div>

Or, you know, not.

(Welcome to me, unplugged and only somewhat toned down. I usually reserve these commentaries for private conversations and the LTD summit, but I figured I would give them a break this time around.)

## If you were following best practices...

If you were following today's 'best practices'...

Wait, what are today's best practices? Because last I heard, TDD was out, BDD was in, we're using Kanban boards to manage our processes, we're practicing servant leadership, wait self-organization, wait no leadership, wait ... are we back to Taylorism yet?

And let's not even try to dig into which software frameworks we should be using, because that's a whole different level of vi vs Emacs....

Whether we're operating now, 10 years ago, or ten years in the future, which practices we follow are not the issue. The waterfall process does, in fact, work in some situations, despite initial creation as an intentionally flawed [strawman argument][2]. Slamming code into an editor furiously, with no regard for change management, requirements elicitation, or even five minutes of up front planning also works in some situations.

<div style="border: 1px solid #AAAAAA; float: right; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/trent/2004_01_19_20.gif" alt="More kitten" style="padding-bottom: 5px; max-width: 250px;" />
</div>

And hell, some of us even like exploring processes, practices, and all those other things. Think of it like coding against a quantum compiler that never returns the same result twice, and occasionally turns into a spherical chicken for no apparent reason. Because, you know, as if projects aren't tricky enough, they keep staffing them with us developers, and we can complexify anything.

## Ok, but what about the Scrum Kool-aid...

Ok, yes, any process can be implemented poorly. A few years ago people were saying the same thing about RUP, and before that...well, lets pretend there was no before that, we like reinventing things. 

MVC is [over 30 years old][3]. Scrum? [Longer than you think][4]. Any guesses on when we started adding behavior to objects?

Scrum is a starting point, as are most of the defined flavors of Agile. If you previously did more complex processes, then Scrum is going to feel extremely lightweight. If you are used to “Adhoc” \*cough cowboy\* style processes, then Scrum will feel like more work. In either case you will probably feel some pain because Scrum (and even more so, Kanban) expose underlying issues that you didn't have to see before.

## Ok, so what then?

I won't pretend to have all the answers, but what I can say is that mindlessly following any set of practices is going to upset people. Look, you hire a bunch of smart people, they're going to question why they are doing certain things. If they think their time is being wasted, they're going to get annoyed, then dissatisfied, and then new jobs (or a lengthy stay in the hospital, stress ain't good for you people).

I personally don't care for Scrum much, but it does provide a good starting point without too many rules. It is different enough that you will be less tempted to fall into your old ways (remember, we want to expose the bad stuff we don't notice), it promotes following good practices (without telling us which ones they are), and it moves the team more towards a collaborative model that forces the developers to realize that everyone else is working hard too.

I'm a developer, I'm allowed to say things like that.

There are going to be things we dislike about any practice, and that's going to be exacerbated if we are pressured to continue following those practices without buying-in. 

> Disaffected programmers write poor code, and poor code makes management add more process in an attempt to “make” their programmers write good code. That just makes morale worse, and so on.

Yep, totally agree, and I think a lot of people would be willing to volunteer my experience with similar issues over the years. I do tend to go on....

## Right, you were solving this, weren't you?

So before I sidetracked, I was offering a solution. Probably one of the best practices I have learned in my career is the practice of continuous improvement, particularly [Deming's Plan-Do-Check-Act][5] process.

<div style="border: 1px solid #AAAAAA; float: right; font-size: 80%; color: #666666; text-align: center; padding: 8px; margin: 8px;">
  <img src="http://tiernok.com/trent/2004_02_17_25.jpg" alt="Yet More kitten" style="padding-bottom: 5px; max-width: 250px;" />
</div>

Evaluate something you think isn't working, try an experiment, check how it went, deploy what worked to the wider company/group/etc. Rinse, Repeat.

And this is where transparency, communications, and a consistent, repeatable process come in. Scrum offers a good starting point, but so does RUP, CMMI, or even _whatever-is-next_ process. Get it working consistently, then start customizing towards your company's needs and skills.

Keep in mind, if you knew exactly what your end process was going to look like, you could just go ahead and implement that. You don't. I often suggest if you're using a defined practice, start without customizing too heavily. That way there's already umpteen-billion books on the subject, training classes, a vocabulary, etc. Figure out the vanilla version, experiment and shape it from there.

Developers are people. Treat us like a cog and we will feel like a cog, engage us, we will be passionate.

 [1]: http://radar.oreilly.com/2011/05/process-kills-developer-passion.html
 [2]: http://en.wikipedia.org/wiki/Waterfall_model "Waterfall Model at Wikipedia"
 [3]: http://c2.com/cgi/wiki?ModelViewControllerHistory "More info on MVC"
 [4]: http://scrum.jeffsutherland.com/2010/08/mike-beedle-on-early-history-of-scrum.html "Early history of scrum"
 [5]: http://en.wikipedia.org/wiki/PDCA "PDCA at Wikipedia"