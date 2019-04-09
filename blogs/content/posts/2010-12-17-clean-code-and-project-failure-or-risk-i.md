---
title: Clean Code and Project Failure (or Risk is not Boolean)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-12-17T12:11:54+00:00
ID: 973
excerpt: I recently tuned into an ongoing debate in the software craftsmanship world on the main cause of project failure. Most people responding to the original request for opinions on twitter pointed to business as the key failure point of software projects, specifically areas like scheduling, budgeting, and understanding requirements. The remainder were examples of bad architecture or poor coding practices sinking the project (or in come cases the company).
url: /index.php/itprofessionals/ethicsit/clean-code-and-project-failure-or-risk-i/
views:
  - 8052
rp4wp_auto_linked:
  - 1
categories:
  - Ethics and IT
  - IT Processes
  - Project Management

---
<div style="float: left; padding: .5em; margin: 1.5em .5em .5em 0px; border: 1px solid #dddddd; color: #666666; font-size: .8em; text-align: center; position: relative;">
  <img src="http://tiernok.com/LTDBlog/toolbox.jpg" alt="Toolbox" height="200" /><br /> Toolbox photo, by way<br /> of <a href="http://www.flickr.com/photos/98897452@N00/1262659991/in/pool-390517@N21/" title="See original">Flickr</a>
</div>

I recently tuned into an ongoing debate in the software craftsmanship world on the main cause of project failure. Most people responding to the original request for opinions on twitter pointed to business as the key failure point of software projects, specifically areas like scheduling, budgeting, and understanding requirements. The remainder were examples of bad architecture or poor coding practices sinking the project (or in come cases the company).

There have been a number of excellent posts on this topic and I urge you to check them out:

  * Bob Martin ([blog][1]|[twitter][2]) &#8211; [The Cost of Clean Code?][3]
  * Michael Norton ([blog][4]|[twitter][5]) &#8211; [Code as a Cause of Project Failure][6]
  * Michael Dubakov ([blog][7]|[twitter][8]) &#8211; [Do Really All Projects Fail Because of Code?][9]

These are all excellent points, but the focus on business vs code has caused us to miss the fact that all of the examples lead to a single root cause that is neither business nor code.

## Say What?

The business is to blame, understanding the business (requirements) is the primary killer, and poor code and architecture can kill your project dead. The management of a specific project led it into the ground, a budget was poorly estimated, and a codebase was too inflexible to deliver what the customer really wanted. The coding started without a design, started a design without understandable requirements, and delivered a solution that left the customer bewildered.

The key ingredient to all of these is the same thing; complexity and our failure to mitigate that complexity. 

## Where Complexity Comes From

Humans follow complex, organic processes that are often defined more by the exceptions than the standard. Communicating this process is challenging and prone to error, and that's before we notice how many steps we miss and take for granted. Compounding this is the fact that we aren't just developing a complex, logical application to replace or enhance a complex, organic process, but we are using <u>other</u> complex, organic processes to do so, such as requirements analysis, project management, software development, budgeting, and so on.

<div style="max-width: 520px; padding: .5em; margin: .5em auto; border: 1px solid #dddddd; color: #666666; font-size: .8em; text-align: center;">
  <img src="http://tiernok.com/LTDBlog/process.jpg" alt="Toolbox" /><br /> The sum of two organic processes is not a neat, easily digitized process flow
</div>

The more complex a problem is, the easier it is to get the answer wrong. The total sum of complexity in a software project is so incredible that it is hardly surprising that so many solutions fail or end in mediocre results. However we can make that complexity work for us by building in a certain level of resiliency; Processes or artifacts in one step of the process can help overcome gaps or failures in others. 

Project success is not a boolean matter, it is a complex, organic, human one.

## But We Were Talking About Clean Code…?

Ok, so coming back down to Earth for a moment, what does all of this have to do with clean code?

As I mentioned above, projects are somewhat resilient. A single failure will rarely sink the entire project. The arguments for clean code are that it is easier to understand, easier to change, cheaper to maintain, and so on. All of this adds resiliency to the coding portion of the process. Clean code makes it cheaper to adjust to changes in other portions of the project, reduces the impact of those changes, and continues to minimize those costs as the project progresses. The best case is to never have to make a change, but the next best, for those of us without magic pixie dust, is to minimize the cost of those changes.

So the lack of clean code is not, in and of itself a failure, it is an increase in risk which indicates a willingness to accept a higher cost for each change. The slower or more expensive the changes, the easier it is to get bogged down, behind, or cancelled.

> Managing complexity is the most important technical topic in software development. In my view, it’s so important that Software’s Primary Technical Imperative has to be managing complexity.
> 
> <div style="text-align: right">
>   Steve McConnell, Code Complete
> </div>

Ok, so I'm not the first to point out the complexity inherent in development, but we can project this into other processes in our projects as well:

Project Management, in all of it's flavors, exists to help manage the complexity inherent in the organic process of executing projects. The adoption of Agile and Continuous Improvement continue to help us fit those processes to our own environments and needs.

To find out how processes work, Lean Manufacturing emphasizes the importance of going and seeing with our own eyes. This helps mitigate the complexities introduced by having someone explain their own process and needs through several layers of communications. This technique helps mitigate risks in analysis, definition, and improvement efforts. 

Software Architecture, Agile Modeling, … we acknowledge that the complex, organic process an end user follows does not translate one-for-one into a software product and that the high level design of a project needs to react and reflect the refined or changing needs of the end user over the course of the project.

And so on.

## So…Who do We Blame?

Projects are big, organic beasts of intertwined complexities. It's difficult to even consider how to put a single number on the risk inherent in executing a project, it's level of resiliency to failures or misunderstandings at any level, and the direct benefits derived from following good practices. 

Success and failure are not boolean, there are many levels of success, mediocrity, and failure.

<div style="max-width: 520px; padding: .5em; margin: .5em auto; border: 1px solid #dddddd; color: #666666; font-size: .8em; text-align: center;">
  <img src="http://tiernok.com/LTDBlog/successbar.png" alt="Success is not Boolean" /><br /> Success and Failure are not Boolean
</div>

Only after a project is complete can we judge whether we believe tactics like producing clean code affected our level of success or failure. Ultimately the blame for failures, mediocrity, and opportunity costs lies with the person decided it was acceptable to forgo processes designed to best mitigate complexity and position the project for success. Whether this person is responsible for the entire project, a key piece of the process, accepting a project that is not financially viable, or simply a developer on one portion of the project, the decision to operate at a higher level of risk means accepting a greater chance of failure.

Success and failure are not as simple as some would make them out to be. Writing clean code is just one way we can help make our projects more resilient. More resiliency may cost the bottom line when we finish a project, but it costs far less than not finishing the project at all and could possibly have paid for itself along the way.

 [1]: http://thecleancoder.blogspot.com/ "Bob Martin's Blog"
 [2]: http://twitter.com/unclebobmartin "UncleBobMartin on Twitter"
 [3]: http://thecleancoder.blogspot.com/2010/10/cost-of-code.html "The Cost of Clean Code?"
 [4]: http://www.docondev.com/ "Doc on Dev blog"
 [5]: http://twitter.com/docondev "DocOnDev on Twitter"
 [6]: http://www.docondev.com/2010/10/code-as-cause-of-project-failure.html "Code as a Cause of Project Failure"
 [7]: http://www.targetprocess.com/ "Edge of Chaos Blog"
 [8]: http://twitter.com/mdubakov "MDubakov on Twitter"
 [9]: http://www.targetprocess.com/blog/2010/12/do-really-all-projects-fail-because-of-code.html "Do Really All Projects Fail Because of Code?"