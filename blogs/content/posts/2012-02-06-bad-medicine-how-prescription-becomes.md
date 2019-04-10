---
title: Bad Medicine – How Prescription Becomes a Problem in User Stories
author: Alex Ullrich
type: post
date: 2012-02-07T01:30:00+00:00
ID: 1500
excerpt: "For almost as long as I've been at my current job, we've been pushing for some kind of acceptance criteria from our QA group, both to serve as a contract for what we are estimating and to make the handoff of stories from dev to QA smoother.  With the mo&hellip;"
url: /index.php/itprofessionals/ethicsit/bad-medicine-how-prescription-becomes/
views:
  - 4681
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT

---
For almost as long as I've been at my current job, we've been pushing for some kind of acceptance criteria from our QA group, both to serve as a contract for what we are estimating and to make the handoff of stories from dev to QA smoother.  With the most recent large set of functionality we've been working on, we've been getting these much more consistently.  We don't always have the criteria at time of estimation, but we have been getting them by the time we work on the story.  So we haven't gotten the benefit of a contract for estimation, but the transition of stories from our team to the QA team has been going much more smoothly.

As this most recent release went on, I've been feeling more and more like I'm working from a spec.  My work has felt much less like a creative process, and more like I'm following orders.  This is certainly not what I look for in a job, and probably not what people had in mind when creating the XP process.  Because of when it started, I've been thinking that maybe we made the wrong decision pushing for the acceptance criteria.  I thought the additional precision might be what is giving me this 'spec fatigue', but when [Jak Charlton][1] mentioned on twitter the idea of prescriptive requirements, I realized how wrong I've been.

The precision offered by the acceptance criteria is almost without question a good thing.  In addition to the contract it provides, the smooth transition between dev and QA is invaluable.  What is a problem is the increased precision with regard to implementation that has crept into our most recent set of stories.  It's still an issue of precision, but where the precision is encountered is the key.  Our most recent stories have not been any more precise when it comes to identifying the user's needs, but much more precise when it comes to implementation details.  These 'prescriptive stories' seem to be the root cause of the 'spec fatigue' I've been experiencing.

I think our main problem is that being told how to solve a problem naturally limits the search area for a solution.  Software development needs to be a very creative process, and when trying to work a solution into a mature codebase few stones should be left unturned in the search for an optimal solution.  If you focus the search on a single stone, you'll rarely find what it is you're looking for.  Even worse, a prescribed solution might not (and often doesn't) make sense when looked at from the guts of the application.  This forces convoluted logic on developers, and if the prescribed solution involves something often accessed in code, it can lead to seemingly unrelated bugs pretty easily.

As much as Jak's comment cleared things up in my head, I can't seem to find a great solution no matter how much I think about it.  We are still not entirely clear on what has made our last few rounds of stories as prescriptive as they are, and without knowing the cause it's hard to propose a solution.  I think the best thing we can do is try to better educate our product team about why we don't want stories to include implementation details.  We have been working on this, trying to help them understand how even though it seems like suggesting an implementation makes our job easier it can really make things much more difficult.  In addition, I think it may be helpful to have a developer or two review the stories as they are being written so we can stop them from going too far down a bad path.  Adding this overhead to our process may not be exactly what we want, but it could really pay off in the long term if we start getting better stories.  Treating the stories as another product could make for an interesting role reversal as well – in addition to better stories we could get a better idea what it feels like to be on the other side of the table in our normal interactions.

 [1]: http://twitter.com/JakCharlton