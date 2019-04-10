---
title: Quality, Quality Assurance, and How Not To Do It
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2013-02-04T12:01:00+00:00
ID: 1957
excerpt: |
  The technical sphere seems to have very mixed definitions for the terms "Quality" and "Quality Assurance". If "It ain't broke" is the highest measure of quality in your company, it may be time to recalibrate expectations.
url: /index.php/itprofessionals/itprocesses/quality-quality-assurance-and-how/
views:
  - 18837
rp4wp_auto_linked:
  - 1
categories:
  - IT Processes

---
The technical sphere seems to have very mixed definitions for the terms “Quality” and “Quality Assurance”. The other day I was reading a post by Devlin Liles ([blog][1]|[twitter][2]) in response to a presentation he had attended ([Quality Assurance–The Team Approach][3]). While I agreed with a lot of his opinions, as I started to leave a comment I found myself sidetracked into examining the different (and in many cases, wrong) definitions we use for Quality and Quality Assurance.

## Quality

Often in the software world, Quality seems to mean “Does it work or not”. Occasionally there is the idea of a [Definition of Done][4], and the definition of Quality includes additional documents, automated tests, formal communications, etc. 

> **Dictionary.com** – character with respect to fineness, or grade of excellence
  
> **Merriam Webster** – degree of excellence, superiority in kind 

If Quality is a scale of excellence, what level do we assign to “it works”? What grade do we get for “I didn't find any broken bits”? Why do we keep assuming “it isn't obviously broken” means we have achieved quality?

I ran into [this post][5] that examines a number of different definitions of quality and refines them all into:

> 1. A characteristic of a product or service that bears on its ability to satisfy and exceed* stated or implied customer needs, and thereby provide customer satisfaction.
  
> 2. Freedom from deficiencies. 

Which is similar to my less formal definition of:

> Quality is how well the product does what we said it would, from the customer's perspective

Quality is a scale, not an on/off switch.

  * If the product has defects (ie, broken bits), then we have Quality problems. 
  * If we cut features at the end of the development cycle and don't tell the customer, we have Quality problems. 
  * If we have a breakdown in communications to the customer while explaining what the product does, we have a Quality problem. 
  * If the customer has difficulty interacting with the product, we have Quality problems. 
  * If the product behaves inconsistently when the customer is using it, we have Quality problems. 
  * If we deviate from our industry standards without informing the customer, we have Quality problems. 
  * If we deviate from their industry standards without informing them, we have Quality problems.
  * If we have to argue with the customer over whether we built what they asked for, we have Quality problems

On a scale of “it destroyed our business” to “it was generations beyond what we could have imagined”, “we didn't find any obvious bugs” sits firmly at zero.

## Quality Assurance

So after a rather long-winded definition of Quality, what about Quality Assurance?

I'm not a quality professional, but most of what I hear or read seems to be using the definition of Quality Assurance that means we have testers testing our product before delivery. Possibly with some automated tests. Possibly even with some automated tests that can be run by the developers (not to be confused with forms of testing that are the developer's responsibility).

Sound about right?

Except this is Quality Ensurance. Testing the mostly finished product is designed to <u>Ensure</u> our product meets a specific standard of quality.

Assurance is a proactive term, “it will be good” vs Ensure's “we checked at the end and it was good”. The purpose of Quality Assurance is to build confidence in the level of Quality (Assure) by driving improvements, so that improved Quality is a natural result. We Assure, are confidant of, better quality by helping it get cooked in from the start.

We are all responsible for Quality, but Quality Assurance is responsible for improving the processes, tooling, environment, and business to increase the Quality of our output. Inspection at the end serves as a good first step, so we can gather data to drive improvements, but if this is all you are getting from your Quality Assurance efforts, then you don't actually have a Quality Assurance effort. 

## Devlin's Post

So back to [Devlin's post][3]. He listed a number of points from the presentation and his thoughts on them. While I didn't attend and am therefore missing some of the context, here are my thoughts.

**1. Risk Mitigation is the main job of QA**

Absolutely not. The job of QA is to improve our ability to build Quality in as we build the product. This may have the side effect of mitigating risk, but that is a natural outgrowth of building something that engenders greater satisfaction from it's purchaser.

**
  
2. Cost of finding bugs is higher as you get later in the deployment cycle, so we should incentivize finding bugs early because we saved that money.**

Incentivising is one way to improve the ability to build Quality as close to the source as possible, so it passes the Quality Assurance definition above. However, I think incentivizing drives the wrong behavior. Even the most honest developer could choose to spend their time finding bugs instead of building the product. And at the end of the day, what we really want is fewer bugs created, which incentivizing could prevent us from improving towards.

**3. High Quality / Focus on Quality cost more money**

Yes, but only in the short term. Producing Quality products is taking the long view, thinking past the immediate contract or deal that is on the table. Measuring the effects can be difficult, but that doesn't mean they are nonexistent. How much would you pay to turn your customers into part of your marketing and sales teams? How much more productive is the team that is proud of what they build? How much less do you spend manually inspecting for Quality at the end of the process when it's built in early on? How much do you save with a better understanding of requirements as your building the product, rather than after you have delivered? What is the value of being able to deliver a product confidently instead of worrying the customer may not accept it? 

Technically, firing the quality team will also save you money. So will firing the development team. That doesn't make them good long term strategies.

**4. Unit Testing should be a task for each development task**

I feel the same as Devlin on this, if you're in an environment that doesn't include it as part of getting a task done, then, yes, it may be helpful to assign each task with a paired “so the unit tests” task until people get used to doing them together. But this isn't an end state, at some point there should be agreement that doing the tests is simply part of calling the work done.

**5.There are Different Kinds of testers**

Here I am going to have to diverge from Devlin again, but I'm pretty sure it's a context issue. I think the question of whether there are different types of testers depends on whether we are talking about types of tests or areas being tested. A manual tester at the end of our process is going to have very different skills then an experienced security tester, which will in turn be different than the skills of a usability tester.

**7. Team Collaboration on testing**

Again, this is an example of one type of improvement a Quality Assurance team could make in a business. This one may be more successful than the incentivising one above, but I think it's going to depend on the environment it's implemented in and, as such, isn't something I can comment on.

**8. QA is the Whole Team’s responsibility.**

I started out agreeing with Devlin, but I'm going to have to revise my opinion. I think the idea of continuous improvement should be built into the whole team, so that everyone takes responsibility for improving their surroundings, processes, and selves. And I think this directly feeds Quality, because the improvements we are making should be improving the quality of things, either as measured by the customer (product quality) or our coworkers, management, or even cleaning staff. So in a perfect world, building in improvements to improve Quality is part of the individual team members responsibilities. 

However, I think there should still be someone who has overall responsibility for QA, either in the role of a manager or coach, to ensure that decisions are being made soundly on real data, ensure we aren't over-optimizing one portion of the business at the expense of another, and so on.

## Thoughts?

A lot of people in the software world dislike Quality assurance, but I think this is because there are a lot of poor Quality Assurance efforts out there that never get past the manual test stage (and thus aren't actually assuring quality).

Regardless, if “It ain't broke” is the highest measure of quality in your company, it may be time to recalibrate expectations.

 [1]: http://devlinliles.com/ "DevlinLiles.com"
 [2]: https://twitter.com/devlinliles "@DevlinLiles on twitter"
 [3]: http://www.devlinliles.com/post/2013/01/31/Quality-Assurance-The-Team-Approach "Quality Assurance–The Team Approach"
 [4]: /index.php/ITProfessionals/ProjectManagement/defining-done "LessThanDot Blog - Defining What Done Means"
 [5]: http://onquality.blogspot.com/2010/04/what-is-quality.html "What is Quality? by Jimena Calfa"