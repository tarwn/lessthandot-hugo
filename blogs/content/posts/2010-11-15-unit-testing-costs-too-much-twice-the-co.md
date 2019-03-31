---
title: Unit Testing Costs Too Much – Twice The Code = Value?
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-11-15T15:56:09+00:00
excerpt: "Last week I had the pleasure of presenting at a .Net Code Camp on the topic of Unit Testing. A key theme of the session was barriers to adoption and the values we can achieve using this tool.The main barrier to my adoption of Unit Testing was the idea that writing twice as much code would increase the value of my work. Twice as much code, as was pointed out in a response to my post two week's ago, sounds like higher maintenance costs, higher initial development costs, and a greater opportunity for bugs."
url: /index.php/desktopdev/generalpurposelanguages/unit-testing-costs-too-much-twice-the-co/
views:
  - 9641
rp4wp_auto_linked:
  - 1
categories:
  - General Purpose Languages
tags:
  - lean software
  - unit testing

---
Last week I had the pleasure of presenting at a [.Net Code Camp][1] on the topic of Unit Testing. A key theme of the session was barriers to adoption and the values we can achieve using this tool.

The main barrier to my adoption of Unit Testing was the idea that writing twice as much code would increase the value of my work. Twice as much code, as was pointed out in a response to [my post two week&#8217;s ago][2], sounds like higher maintenance costs, higher initial development costs, and a greater opportunity for bugs.

## How Does Adding Code Increase Value?

People have published numerous lists of benefits they have received from Unit Testing, but there is still this seemingly illogical starting point of creating more code to deliver the same end product. And before evaluating any other benefits we could achieve from Unit testing, we have to start by resolving that seeming contradiction.

### Current Process

Consider a software process that includes developers, a QA department, a customer review, and a production deployment. 

  1. Each developer writes code to fulfill assigned features or tasks
  2. Once enough pieces have been written, the developer can wire them together and debug the pieces they have written
  3. Completed pieces are handed off to QA
  4. QA tests those pieces against the requirements
  5. Bugs (code defects or missing requirements) are returned to developers
  6. Once QA is satisfied, the pieces are handed off to the customer for review
  7. The customer reviews the pieces, possibly sending back instructions for refinement or corrections
  8. Once the customer is satisfied, the pieces are deployed to production
  9. End users use the pieces in production and interact with production data

This won&#8217;t match everyone&#8217;s environments but it should be simple enough to be representative of the work that happens in our environments.

### Unit Testing is Testing at the Source

In Lean Manufacturing there is the concept of moving quality to the source of production. Tests are created that are quick to execute and provide the operator with a quick detection of defects instead of waiting for a Quality Control at the end of the process.

Moving testing closer to the source reduces the number of defective parts that are stored, transported, and used in later production steps. It allows for faster reaction time to correct the problem and reduces the number of interruptions from having to stop a run and re-produce product for a previous order.

All of these costs have well-defined values in manufacturing environments as well as parallels in our own environments.

### Lean Development

Each step in our sample development process above increases the base cost of producing a working feature. The further a bug makes it without being detected the greater costs it has accrued and the more cost it will take to correct it.

<div style="text-align: center; color: #666666;">
  <img src="http://tiernok.com/LTDBlog/unittesting/UnitTestingGraphs.png" alt="Effort and cost of bugs increase dramatically as time passes" /><br /> Effort and cost from bugs increase dramatically as time passes
</div>

Costs:

  * Bug while writing one unit: Cheapest cost
  * Several pieces wired together: changes to the unit and surrounding units
  * During Handoff: Wasted meeting/handoff time for devs and QA personnel, potential schedule change, additional debugging time and cost changes (per prior step)
  * Bugs found in QA: (at minimum) Handoff to Dev, switch from new task to work on bug, investigation, development, debugging changes, handoff to QA, QA repeats some level of testing (high potential for new bug, developers tend to test direct changes only)
  * Bugs found in handoff to customer: (at a minimum) Prior QA steps,time for QA to duplicate problem, task-switching time for QA, cost of customer time and others involved, re-handoff to customer, timeline impact, potential impact to customer trust (depends on frequency &#8211; costs include increased micro-management, increased meetings, etc)
  * Bugs found by customer: Similar to last step but any testing to this point by customer is likely to be duplicated, customer schedules impacted, cost of extar customer time, timeline impacted
  * Bugs found in production: Prior step + developers are pulled off other tasks to detect and cleanup bad data, schedules are impacted due to developer time, potentially wasted end user time, potential to retrain + monitor users to work around problem and then retrain back afterward

Besides people-based costs, there are also hidden costs to returning to development and quality stages. The more time that passes between writing the code and returning to it, the more familiarity will be lost and the higher the potential that other pieces will have been added on top of the bug. Other details, such as the specifics of the requirements that code was fulfilling and how it was initially tested prior to QA hand-off, will rely on the memory of the developer and will have to take into account any changes that have occurred since initial development. Perhaps of greater concern is that QA will not have the same level of familiarity with the tests they initially performed and the customer is more likely to add even more &#8216;refinements&#8217; when it comes time to re-review the changes.

Unit Testing is our tool for doing testing at the source, to catch defects earlier before they propagate costs throughout the process.

## The Value of Quality at the Source

Unit Testing will not prevent all bugs but it will catch bugs earlier, reducing the costs above. Unit Testing starts earlier, allowing us to test each unit we build instead of waiting until we have 10-20 built and wired to an interface. For the situations where a bug still makes it past development, we will have a solid set of tests to run after making corrections so we can not only test changes but also the rest of the codebase, heavily reducing the risk of creating new bugs out of bug fixes.

The value we gain from Unit Testing is value to the team and the business, not to the individual developer (though there are values to be gained there as well).

## Balancing Cost and Value

There is a cost to writing Unit Tests that we have to balance with the potential savings and risk reduction. I don&#8217;t believe there is a universal answer as to how much Unit Testing is appropriate for a given environment, project, and team. Many teams take it on faith that 100% coverage is their best bet, but I am not convinced. 

What I can tell you is that all of the benefits of this tool are out of your reach if you don’t give it a test drive. 

As several people saw in my CodeCamp presentation, it doesn’t take long to add a Unit Testing project to an existing application and build a few tests. Pick a particularly complex business function or an existing bug in need of correction. If you decide to start evaluating it for greater implementation, the list of costs above should provide some pointers on where you can create some measurements to determine the level of value you are receiving.

Other posts in this unplanned series:

  * Initial &#8220;Unit Testing Costs Too Much&#8221; post: [Unit Testing Costs Too Much][3]
  * Code camp review and links for slides: [Raleigh Code Camp Followup][4]
  * 2x Code Followup: [Unit Testing Costs Too Much &#8211; Twice The Code = Value?][5]
  * Too Many Things to Learn: [Unit Testing Costs Too Much &#8211; Too Many Things to Learn][6]

 [1]: /index.php/DesktopDev/MSTech/raleigh-code-camp-followup "Read my quick CodeCamp review"
 [2]: /index.php/DesktopDev/GeneralPurposeLanguages/unit-testing-costs-too-much "Read the 'Unit testing Costs Too Much' post"
 [3]: /index.php/DesktopDev/GeneralPurposeLanguages/unit-testing-costs-too-much "Check out the first post"
 [4]: /index.php/All/?p=999 "Code Camp review"
 [5]: /index.php/DesktopDev/GeneralPurposeLanguages/unit-testing-costs-too-much-twice-the-co "Read more on the 2x Code topic"
 [6]: /index.php/WebDev/ServerProgramming/unit-testing-costs-too-much-too-many-thi "Read more on the Unit Test Cost topic"