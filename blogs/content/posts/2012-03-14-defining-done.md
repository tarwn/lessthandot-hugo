---
title: Defining What "Done" Means
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2012-03-14T12:12:00+00:00
ID: 1553
excerpt: As I left college, I believed that projects would be clearly defined in a functional specification (despite my actual experience up to that point). The grand purpose of the project would be detailed, the goals and assumptions would be written and indexed, and the expectations for uptime, performance, and similar non-functional requirements would be included.
url: /index.php/itprofessionals/projectmanagement/defining-done/
views:
  - 4091
rp4wp_auto_linked:
  - 1
categories:
  - Project Management

---
As I left college, I believed that projects would be clearly defined in a functional specification (despite my actual experience up to that point). The grand purpose of the project would be detailed, the goals and assumptions would be written and indexed, and the expectations for uptime, performance, and similar non-functional requirements would be included. In short, I would know exactly what needed to be produced in order to call the project "complete".

A little later I was introduced to the idea of a project charter, a brief 2-page document that simply outlined the goals and expectations of a project, a _"what will you have when I'm done"_ document. This did a better job, throwing out the crystal ball and admitting that it only knew the goals and some constraints, not the details of how it would look when we got there. 

At some point I was introduced to XP user stories and defining the user's expectations as acceptance criteria at the feature level, focusing on small distinct pieces of work and providing the necessary level of detail at the last responsible moment.

And at some point, after an on-again, off-again relationship with unit testing, I was able to use TDD and BDD on projects, both of which work at even lower levels, defining what the behavior will be of a feature or code unit when it's code complete.

## Definition of Done

The consistent thread through the lessons above was defining what "done" means at a project, feature, behavior, or code unit level. 

A requirements document provides a very deep definition of what the project will look like when it is complete. Unfortunately this only works if you will have a limited need to make changes along the way. Often what we think we need up front requires some hands on use to see what we overlooked, whether the hypothesized feature helps us meet the goal as we expected it to, and whether it makes to our end user.

A simple project overview, like the charter above, can provide the overall goals and the general shape of the solution, without trying to dive too deep into the mechanics of how they will be achieved. It defines the goals of our project, serves as a measurement to determine if we are done, and helps prioritize the features we are going to implement.

User Stories operate at a lower level, defining a single feature and what the end users expectations are for that feature. User stories detail expectations from the end users perspective, so discussing or prioritizing them with an end user occurs in terminology they are familiar with. User Stories provide us with the "Why" and room to ask deeper questions.

With BDD or TDD we define what done means at an individual interaction or code unit level and can then create code that meets that definition. They provide confirmation that we have built exactly what we have set out to build, that we have met our definition of done, and that we will have some protection from derailing that work later on. 

## Multiple Facets of Done-ness

As critical as it is to understand what the customer or end user is actually asking us for, having a good definition of "Done" doesn't stop with the customer's direct expectations. We also have non-functional requirements that apply to large portions of the project as well as internal processes and practices, all of which should be considered when defining what "Done" means at a specific level.

### Non-functional Requirements

Non-functional requirements span across many or all of the features we build into the system and can be difficult to track. Part of creating a definition of "Done" for a feature should include scanning the list of known non-functional requirements and adding the appropriate ones to the list. 

Building an AJAX feature on a web page that talks to a 3rd party web service? Probably need to include the general requirements around responsiveness of the browser page and how to degrade or error out the service when the 3rd party service inevitably goes missing.

### Process and Practices

We probably have a process we want our features to follow as we build them. In some cases this might be so easy it doesn't need to be included, in others we might have a more complex process we need to follow. A feature isn't done at "coded and thrown over the wall", if there are quality steps, levels of unit test coverage, static analysis to prevent us from dirtying up the codebase, and so on, those should be included in our definition of "Done".

Along with our ongoing process, we can also use the definition to drive longer term changes into our process or codebase. Want to add test coverage to a legacy project? Add a requirement that completing a new feature requires meeting a target test coverage and 3 tests in an older part of the code base. 

Being "Done" with a feature means the feature is ready for delivery and we won't be coming back to it in 6 months to try to bolt-on performance improvements to meet metrics we knew about in the beginning.

## Benefits of a Definition

Why are we spending so much time on something that seems like just extra work?

There's a difference between _"I know what we have to do, lets just go do it"_ and having a good definition of "Done". If it is truly that obvious, making a quick bullet list will only take about 5 minutes. If it takes longer, it wasn't as obvious as we thought and we've already uncovered some benefit.

**End User Context:** Having the end end user define their expectations helps uncover the parts that are obvious to them, but foreign to us as IT people, employees of a different company, whatever. This also helps us build something that better fits the end users needs and expectations and provides us with information we need to help them make good technical decisions.

**Focus:** With a definition of done, we have a laser focus on what needs to be done. Instead of building a general solution to meet what we think they think they want, we can build directly towards their criteria. That's not to say we're going to get it perfect on the first iteration, but we can build a leaner solution that more directly meets their expectations. This means fewer iterations and corrections with a smaller and easier to modify code base.

**Get Stuff Done:** Ever get on one of those projects where it feels like it just kind of keeps on sluggishly flowing past and tasks are only done if no one has asked about them in a couple weeks? Yeah, I've been there too. Having a definition of done not only helps you better define when it hits that magic "Done" moment, but also keeps those one day tasks from turning into 6 month, under the radar, mini-marathon projects. Want an additional feature on top of the original one? We can totally do that too, but lets make a task for it so I can make sure I understand and capture your expectations. That other task is done, done, done.

**Prioritization:** How important is "add a button and text input to screen X"? Yeah, I don't know either. So how are we going to get the end user to help prioritize these things without someone pulling out the "they're all number one" phrase? Instead of trying to prioritize screens and buttons, the end user is prioritizing their own needs and expectations. This can help the end user prioritize, consider scope reduction of less necessary features, or potentially modifying a deadline to get all of the truly critical elements.

**Bake Enough Quality In:** Whatever your non-functional or process criteria are, trying to add them at the end of the project rarely works out well. If you need a certain level of performance, make this part of the criteria along the way. Afraid the focus will shift from getting things done to premature performance tuning? Create a looser "acceptable performance" level that is used during the main development, then tune from there afterwards. We don't want to waste time on premature optimization, but without at least a minimum acceptable bar you will quickly find out just how horrendously slow something can be written to run.

**Don't Bake Too Much Quality In:** A lack of definition doesn't automatically make everyone faster, the opposite can happen as well. Without a definition, each person on the project (and the customer) is left to define their own level for non-functional or process criteria. These are not likely to be the same. So the customer has one non-communicated level of performance expectations, the project manager has another. One developer is slamming code out as fast as they can with "as long as it compiles" as a performance bar, another is spending days trying to squeeze an extra 10ms out of a process. Defining "Done" in this case not only helps raise the bar and communicate consistent expectations, it also frees up time from any of the members of the team that have more stringent personal definitions they fall back on. 

## "Done" for the post

This post was inspired by posts from Howard (not published yet?) and Alex ([Bad Medicine – How Prescription Becomes a Problem in User Stories][1]) as well as ongoing conversations we've been having. 

It may seem like wasted effort to put a definition of "Done" together, but I have yet to see an environment where the long term cost wasn't much greater (and my personal IT experience spans consulting engagements, 4 SaaS products, and over 75 projects). Not every website needs to load at Google search speeds, but the team needs to have a clear bar to aim for, even if that is "under ten seconds", and the customer needs to know and be OK with this bar. 

A definition of "Done" doesn't need to have crystal clear requirements in infinite detail, "web-scale" performance guidelines, and so on. Be pragmatic. It's more important that the end user expectations are captured and understood, that the relevant non-functional requirements (when the 3rd party service goes down, so does our entire product, and it's OK) are included, and that somewhere we have captured the teams expectations for practices and process. Aim for just enough and be aware you will still have surprises.

 [1]: /index.php/ITProfessionals/EthicsIT/bad-medicine-how-prescription-becomes "Bad Medicine - How Prescription Becomes a Problem in User Stories"