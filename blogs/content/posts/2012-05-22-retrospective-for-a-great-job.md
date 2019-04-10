---
title: Retrospective for a (Great) Job
author: Alex Ullrich
type: post
date: 2012-05-22T10:51:00+00:00
ID: 1624
excerpt: "It was a hard decision, but I recently left what was the best job I'd ever had in my life.  A lot of what made it great was the people there, but a big part of it was the way we worked as well. Since frequent retrospectives were such an important part o&hellip;"
url: /index.php/itprofessionals/ethicsit/retrospective-for-a-great-job/
views:
  - 4265
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT

---
It was a hard decision, but I recently left what was the best job I'd ever had in my life.  A lot of what made it great was the people there, but a big part of it was the way we worked as well. Since frequent retrospectives were such an important part of keeping us a well oiled machine, I figured I should have a self-retrospective on my time there.  This has really helped me identify what I want in a job, not necessarily in terms of subject matter but in terms of process.  Some of the things I loved about the job were very special, and things that I'm not likely to find in another job \*ever\*, but others are things that just about any team can do.  That last part made it seem worth sharing.

## The Hard Stuff

There are some things about the old job that truly felt special.  The first was the close interaction with the QA and product groups that we enjoyed.  Most of our product team located on the other side of the ocean but they were always available in the morning and willing to answer any questions.  The QA group on the other hand was colocated, and sat almost uncomfortably close to us at times.  As a result they were able to discuss and replicate bugs for us with relative ease, making it much easier to get defects fixed.  I realize this isn't \*that\* difficult to obtain in most organizations but the fact that it's outside of a team's control is what lands it in this section.

The really special part (and the part I **really** hope to get a chance to do again) was pair programming.  I was skeptical at first but by the time I left my interview (which included an extended pairing session) I was sold.  After living it for a while, I truly believe that there isn't a better way to deliver software consistently than pair programming, especially if it's done in conjunction with TDD.  Most of this is due to the fact that all production code naturally ends up subject to a realtime code review – something that happens naturally when pairing.  The conversation this creates tends to draw out optimal solutions more quickly, and identify potential issues with proposed solutions earlier in the process.

Pairing also brings shared code ownership as a natural side effect.  This prevents any extreme specialization, and leaves the product in a state that **anyone** on the team can work on a given problem.  Knowledge becomes the property of the group, not of an individual who could leave at any time.  In my experience pairing also helped to get new hires up to speed more quickly, and made [interviewing much more effective.][1]

## The Low(er) Hanging Fruit

In a world where most of what we deal with is out of our control, it's important to exert control over our circumstances whenever possible.  We may not be able to control an ever-growing backlog of feature requests, but we can have a voice in the planning and scheduling of the work.  There may be some growing pains involved but any team should be able to take some control over this part of the process.  I would go so far as to say it's a sign of organizational dysfunction if they can't.

The most important thing seems to be moving away from time-based estimates.  While estimates based on time can create the illusion of control for managers and PM's, I have never seen time based estimates lead to a consistent throughput on a team.  Using point-based estimates helps stop people from agonizing over number of hours and estimate more quickly, and also tends to make it easier to establish a consistent velocity because it's not based on the number of hours in the week.  [Riaan Hanekom][2] has a blog post called [The Humble Story Point][3] describing this better than I ever could.

It also helps having all stakeholders involved in the planning meeting.  Once they are involved, it is important to empower everyone present in the decision making process.   This can lead to some protracted (and occasionally frustrating) discussions, but that is usually a sign that something is missing in the requirements.  The importance of discovering issues like that **before** starting to work on them and getting all stakeholders on the same page when it comes to what is actually being estimated can't be emphasized enough – few things are as demoralizing as delivering some work and finding that the product owner or QA group had a far different understanding of what was to be delivered.

Playing planning poker when the story has been discussed keeps the planning session lively and interesting, and wide gaps between team member estimates provide a good starting point for discussion of technical details.  Most of the time when estimates vary widely within the team it is because of a discrepancy between team members' understanding of the existing code, and the degree of change that the new story requires.  Planning poker provides a good format for this kind of discussion.

Using story points in conjunction with planning-pokeresque estimation meetings seems to really help keep all stakeholders engaged in the process of estimation and prioritization, and eventually leads to the holy grail that is predictable output.  Achieving a predictable output is **much** easier said than done, but it is definitely something we can strive for.  It allows teams to make a hard commitment to product owners regarding what is delivered from an iteration – and from a developer's perspective meeting this hard commitment helps to keep morale high.  This leads to a much more sustainable process than one in which people are killing themselves to meet outlandish commitments every iteration.

Once iterations have been planned, it is really important that their definition not change.  Changing the content of an iteration while it is being worked can really frustrate a team, because the context switching involved can cause a lot of friction, and it usually reduces the amount of work that can be done in an iteration.  It can also cause an iteration to spill outside the time alotted for it, negatively effecting future iterations.  It also becomes more difficult for a team to shake off the negative feelings resulting from missing a commitment if the iteration is a moving target – so those negative feelings can linger and become real morale issues.  A policy of discipline within iterations tends to lead to much greater flexibility between them – an admission that few things are so urgent a team **needs** to drop everything to fix them tends to help cultivate this discipline, which in turn makes it easier to react when there is a real emergency.

Finally, a team needs to have something in place to reflect on their process and adjust when things are going wrong.  I'm not sure they are needed for each iteration but frequent retrospectives are a very good tool for this.  It can be difficult at times but its important not to blame anyone when discussing process problems – Kerth's [prime directive for retrospectives][4] can be a valuable tool for accomplishing this goal.

## Conclusion

I guess I should stop rambling at some point, and now seems as good a time as any.  From a technical perspective I learned a lot at this last job, but it pales in comparison to what it taught me about process.  I've been on teams that were pretty well oiled machines in the past, but never one that managed to stay operating at a high level for quite so long.  While I want to credit the people, and some of the truly special elements of our environment for the amount of success we had, I truly believe it is our discipline when it comes to the more mundane parts of our process that enabled us to stay successful.  Hopefully I'll be able to remember this if I'm unfortunate enough to be in a more “normal” environment in the future.

 [1]: http://buildbroke.com/2011/02/10/pairing-and-interviewing/
 [2]: https://twitter.com/#!/rhanekom
 [3]: http://riaanhanekom.com/blog/2012/02/13/the-humble-story-point/
 [4]: http://www.retrospectives.com/pages/retroPrimeDirective.html