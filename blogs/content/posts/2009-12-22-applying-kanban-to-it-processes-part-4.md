---
title: Applying Kanban to IT Processes (Part 4)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2009-12-22T11:01:39+00:00
url: /index.php/itprofessionals/projectmanagement/applying-kanban-to-it-processes-part-4/
views:
  - 19149
rp4wp_auto_linked:
  - 1
categories:
  - IT Processes
  - Project Management
tags:
  - kanban
  - measurement
  - process improvement

---
_This is the fourth article in a multi-article set that describes the basics of Kanban and explores applying Kanban to IT Processes. [Part one][1] provides a basic overview of Kanban and how it is used in manufacturing. The remaining parts explore sample scenarios to help generate ideas for your own environment._

<div style="background-color: #eeeeee; padding: 1em; font-style: italic">
  Several times in the past week I considered rescheduling this article, but despite overtime, a dead car, potential snow and ice, a virus infection on my wife&#8217;s computer, numerous small devices failing at home and work, and finally a power outage, we have still managed to get it published. Unfortunately the drawings are missing some minor touches, as the power outage happened to coincide with the &#8216;adding of the magnets&#8217; in photoshop.
</div>

In part four of the &#8216;Applying Kanban&#8217; series, we are following a software development team. They currently follow a process that has been referred to as a [sashimi model][2], a modified waterfall model that incorporates overlapping phases of design, requirements, development, and testing. This example focuses on using Kanban to enhance and refine a current process rather than execute a complete process conversion.

## Welcome to GHI Inc

GHI Inc is a small development company that focuses on retail inventory and logistics systems. Their business focus is building and integrating systems that manage communications and transaction processing for [B2B][3] and [B2C][4] integration. One of their current challenges they have chosen to focus on is tracking and reporting progress. Because their products require a great deal of business logic and back-end processing and very little front-end interface, the customer cannot see the growth and development of the product. Improving the tracking and reporting mechanisms will provide the customer with more information on how the project is progressing, increasing their confidence level in both the solution and the company. The company believes that improved confidence will not only affect the relationship with this customer, but improve their image in the marketplace with other potential customers as well.

### Current State

The team&#8217;s current process begins with gathering high-level requirements and building a general architecture, without any of the detailed business rules or logic. Once the skeleton is developed, the team focuses on iteratively gathering requirements and building the more detailed modules necessary to add flesh to the skeleton. The overall systems design follows an N-tier approach, with defined layers for data access, business logic, separate transaction and translation processes for each external system, and a limited user interface. The iterative focus on detailed modules allows discovery of the deeper business logic and requirements to occur relatively close to the development against those requirements. This modular approach also allows portions of the system to be test-driven against real third party systems without requiring the entire solution to be complete. These test-drives also allow the customer&#8217;s technical staff and business analysts to get involved and find conflicts earlier.

The team has been challenged with providing better visibility and clear measurements that can be shared with the customer. While many of the team members are interested in trying Agile methods, such as Scrum or XP, the team decides that applying Kanban to their initial process will be a smaller change from the customer viewpoint. Once they have better visibility and measurement they intend to re-evaluate the potential of using Scrum or another Agile methodology, as they feel a progression would be acceptable to the customers where a complete process changeover may not.

As part of the ongoing efforts, the team is also looking for a way to improve their delivery speed. Most of the team agrees that an additional developer would speed up their delivery process by at least 15%, but they won&#8217;t be able to make the case for additional headcount without some convincing metrics or measurements that support their claims.

## Defining the Process

Over the course of several long meetings, the team builds a definition of their process as a series of clearly delineated steps that they follow in cycles, iteratively focusing on smaller pieces. 

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <img src="http://www.tiernok.com/LTDBlog/Pt_4_Sashimi.png" title="Sample Sashimi Process Drawing" /><br /> Iterative waterfall process (Sashimi)
</div>

Though the process they follow is almost fractal in it execution, each finer-detailed pass follows a common series of steps:

Remaining Work
:   Undefined work that needs to be designed and developed

Requirements
:   Gathering business or third party requirements on a task or module

Design 
:   Applying the design standards and model and external requirements to build a design for the task or module

Development
:   Developing to the design and requirements

Testing
:   Testing completed development for defects* and alignment with the requirements

Customer Review and Acceptance
:   Reviewing groups of tasks with technical staff, business analysts, or third party services

The team currently has a project manager, five developers, and a dedicated QA person. After laying out their process, they have decided that one of the developers will be in charge of architecture (requirements and design), the project manager will be responsible for the customer steps and the prioritization of remaining work (with assistance from the architect), and that the remaining developers and QA person will take the development and testing steps. The architect will also be responsible for part-time development if they get ahead on the requirements and design process.

<div style="background-color: #eeeeee; padding: 1em; font-style: italic">
  Defects are imperfections in a product that occur during normal production or operation. Examples of defects in software engineering are mis-typed code, referencing variables incorrectly, and other technical errors. Requirements-level issues are problems like missing options in search filters, incorrect layout of a form, and other items that are directly driven by the customer.<br /> Example: The customer will never specifically ask that a validation function not crash the server, therefore an error in a validation function that crashes a server falls into the defect category
</div>

### Visual Board 1

For visualization and tracking purposes, the team expands several steps into sub-steps. The Requirements and Design step has been defined as a series of three sub-steps, with the last step being a &#8216;ready&#8217; step that indicates tasks that developers can pick up and begin developing on. Development has been split into two distinct sub-steps, a &#8216;working&#8217; step and a &#8216;completed&#8217; step. Items in the &#8216;completed&#8217; sub-step are available for QA to pick up and test.

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <img src="http://www.tiernok.com/LTDBlog/KanbanPt4_VisBoard_4a.png" title="Initial Process Steps" /><br /> The first Visual Board
</div>

The team uses a large magnetic whiteboard as the canvas for their Kanban board. Rather than use swim-lanes to visualize work in progress, they create &#8216;buckets&#8217; on the board that have a places for tasks and for a magnetic holder with the team member&#8217;s picture. For example, when the team architect is gathering requirements for a task they place their picture in the requirements area with the task they are currently working on. The project manager has donated a set of colorful animal stamps which the team members will use to stamp tasks as they complete them, enabling the rest of the team to easily track who has worked on a task. This allows the QA or a follow-up developer to easily communicate back to the original developer without any complex tracking mechanisms.

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <img src="http://www.tiernok.com/LTDBlog/KanbanPt4_VisBoard_4b.png" title="Upadted with Kanban Limits" /><br /> Kanban Limits
</div>

The Kanban limits are created based on the number of people assigned to each step and some rough guesses as to how much work can be at each step without later steps running out of work. The Requirements and Design area is limited to three tasks to balance between keeping requirements information fresh and providing enough work for the development step. The development area has a limit of five tasks to allow the architect to move forward and develop on small tasks when the limit is reached for Requirements and Design. The QA step is limited to a single task, pulled from the completed development tasks. This allows the QA person to complete a round of tests without the complications of new development being deployed in mid-test. The queue of items pending customer review is not limited because the project manager already has regular weekly meetings with the customer to discuss completed tasks. The queue allows the project manager to pick sets of completed tasks to discuss based on the attendees, or to send out early reminders to ensure the correct attendees will be present.

### Executing the New Process

Initially the process is a little rough, as the team tries to finish tasks that are over the limits and flush this work through the system. The architect begins acclimating to a role that requires more time with whiteboards and less with a code editor. The project manager begins making the transition from managing the flow of tasks through the process, to managing the prioritization and improving communications with the customer.

After a few weeks the process is flowing more smoothly. The project manager prioritizes the tasks in the outstanding queue, occasionally involving the architect to ensure the design requirements are taken into account. The architect picks up tasks from the top of the priority stack, gathers more detailed requirements from the customer, and builds an overall design that incorporates the requirements and aligns with the high level design. When the requirements and design step reaches the Kanban limit, the architect move forward to the development step and either helps one of the developers or picks up a small task to complete on their own. The developers pick up tasks from the architecture steps and develop against them. After completing a task they move it to the &#8216;done&#8217; sub-step and either assist one of the other developers or pick up a new task. When the development step reaches its Kanban limit, developers without tasks move forward to assist the QA person in testing in order to clear the roadblock. The QA person tests each task, looking for defects as well as alignment with customer requirements and internal design. 

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <img src="http://www.tiernok.com/LTDBlog/KanbanPt4_VisBoard_4b_QA.png" title="QA Tasks return or move forward" /><br /> Tasks from QA return to Development or move forward
</div>

After testing a task, the QA person either puts it back in the development area to be corrected or moves it forward to the pending queue. Returning a task to the developers will commonly cause the Kanban limit to be reached or exceeded for development. the next developer that reaches a stopping point then either helps continue testing or picks up the returned task and corrects the outstanding issues.

## First Revision

As part of the initial process definition, the team agreed to review and revise their process on a regular basis. During the first few weeks, the project manager has been collects high level statistics to baseline their throughput and provide information for re-evaluation of the process. The detail information includes statistics on how often tasks return from testing, how often the architect or developers move forward in the process to help clear roadblocks, and a general estimation of how the customer reacts in each weekly review meeting.

### Evaluation

There were a number of partially completed tasks when the team started and they expected the QA step to stabilize after they worked through those tasks. Unfortunately this has not occurred and in order to keep up with a regular flow of tasks, developers are often moving forward to help QA clear a backlog. The couple of times the developers had exceptional days, QA roadblocked almost immediately, requiring several developers to help clear the backlog. 

The architect role requires less time than originally estimated, leading the architect to do development work about 50% of the time. This causes nearly every task completed by the architect to require debugging or correction by one of the developers, further reducing the effectiveness of the process by forcing someone unfamiliar with a task&#8217;s requirements and code to quickly troubleshoot and correct it.

### Process Changes

Initially the team believed that the biggest limitation on completing tasks was manpower and that increasing the number of developers would drive a direct increase in the number of tasks they were completing each week. However, after working with their refined and more measurable process, they have found that adding more developers would provide little to no benefit because the current constraint on their process is getting tasks through the bottleneck at QA. If they increase the throughput of this step then the entire process will be able to move more smoothly and produce at a higher throughput, as QA will be able to pull tasks from the development area more quickly.

The team considers several ideas for increasing the manpower at the testing step, including hiring an additional QA person and having the architect work part-time on QA, instead of development. After discussing several options, one team member suggests using the architect and any ongoing developer downtime to build automated tests. The theory is that automating some of the tests will not only reduce the amount of work the QA person is required to do against each task, but will also reduce the number of tasks that are returned from QA to development for debugging and correction. The team decides to try this process change because it requires no additional spending and it makes little negative impact to have the architect working on unit tests instead of helping to complete work against the development Kanban limit. 

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <img src="http://www.tiernok.com/LTDBlog/KanbanPt4_VisBoard_4c.png" title="Revised version of Kanban board" /><br /> Revised Kanban Board w/ Additional Steps
</div>

The Kanban board is modified to include a test-creation sub-step as part of the requirements and design process. The architect is responsible for building a number of test cases for each task as part of the task definition. When the limits for the requirements area are reached, the architect works on building tests against previously completed modules. This ensures that ongoing tasks have automated tests and, as time progresses, coverage grows for testing module integration and any potential side-effects or gaps when integrating the designed modules together. 

The limit for the development step on the board is increased and a small area is created for tasks returning from QA. These tasks are treated as higher priority than tasks from the requirements area to ensure that mostly completed tasks are corrected and completed before new work is started. To help increase the efficiency of developers correcting or debugging code written by others, a new code review standard is put in place. Each time a task is completed the developer reviews the code with one or more of the other developers to ensue it is straightforward, commented where necessary, and follows existing code standards. This process is relatively quick and reduces the costs and extra time involved in troubleshooting tasks someone else originally worked on.

## Final Analysis

While the team will continue to re-evaluate and revise their process every few weeks, they have already achieved a number of improvements over both their original process and the first defined version with Kanban limits.

### Process Flow

The team has not revised their prioritization, requirements, or design process, so these steps continue to work similar to the fashion they did prior to applying Kanban limits. The new process of creating unit tests for the tasks was slow for the first week, but as the architect gained experience with the testing framework and the idea of building tests, the process of building tests has become smoother and has started allowing for time to build tests against previously completed modules. As developers complete tasks they first look in their &#8216;corrections&#8217; queue from QA to see if any tasks are waiting to be debugged or revised. This places priority on previously started tasks and ensures that new tasks are only started when the to-be-corrected area is clear. The new unit testing framework has given the developers a faster way to test their work before pronouncing it completed, as well as providing another dimension of information on the business and design requirements. Unit testing has also reduced the number of corrective tasks the developers have to work on because they are catching most of the defects that QA used to be responsible for. 

The QA step has become much faster and it appears likely that the QA person will need a secondary job soon, as they are not only keeping up with all 4 developers but they have actually had two dry spells in the past week due to being ahead of the developers. The average number of returned tasks has been greatly reduced, with only about 5% of tasks being returned from troubleshooting where over 50% were once being returned. The QA step also evaluates and uses the automated tests to ensure no defects have been overlooked and the time saved from not having to build or manually drive tests against each task is enormous.

With the QA step completing task evaluation more quickly, the queue of items to be reviewed by the customer is growing at a much faster rate. The project manager is beginning to have difficulty covering everything in a single weekly meeting because the number of completed items has grown to be a little unwieldy with the existing meeting format. Modifications to the communications method are likely to be coming in the near future to help manage the increased flow of completed tasks. The overall metrics for task throughput and estimated completion dates have been more consistent and don&#8217;t vary as wildly as they did in the previous process. While the metrics have been reflecting the process improvements, the variation or range of the values has been much tighter.

### Achievements

The team has only been working with their process for a couple months, but they have already started to see positive improvements and they are already considering ideas for their next improvement cycle. Here are a few of the achievements they have already accomplished:

<ul style="list-style-position: outside; margin-left: 2em">
  <li>
    Increased productivity – The overall rate of development has grown when compared to rates prior to the Kanban implementation
  </li>
  <li>
    Increased confidence – The improvement in development speed, usage of automated tools, and new metrics have all contributed to improving the customer&#8217;s confidence
  </li>
  <li>
    Clarity of vision – process definition and the Kanban board have given the team a better view of their process and a way to compare with other methodologies
  </li>
  <li>
    Reduced Estimation Variability – the ability to gauge throughput and better estimates has allowed the company to better gauge the timing on approaching new potential customers
  </li>
  <li>
    Measurability – Good metrics provide an important tool for continuing to improve the process and gauging whether potential improvements have improved the process or not
  </li>
  <li>
    Foundation for Training – improvements in the process have helped decrease costs and provide an argument for using that time for additional training, to help drive later round of improvements or prevent the company from jumping on board with a technology that won&#8217;t deliver what they need
  </li>
</ul>



## Next Week

Next week we will be wrapping up this 5-part series on Applying Kanban to IT Processes with ideas and surface explorations of other areas of IT. We will also discuss some non-human processes that Kanban can be applied to, as well as some brief information on some of the other concepts that have been mentioned in the series (such as continuous improvement). 

If you have missed prior articles in the series you can find them here:
  
Applying Kanban &#8230; Part 1: [Kanban Overview][5]
  
Applying Kanban &#8230; Part 2: [Kanban applied to Tech Support][6]
  
Applying Kanban &#8230; Part 3: [Kanban applied to PC Deployment][7]
  
_Applying Kanban &#8230; Part 4: Kanban applied to a Development Group_
  
Applying Kanban &#8230; Part 5: [Challenges, Additional Concepts, and Wrapup][8]

 [1]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-1
 [2]: http://en.wikipedia.org/wiki/Waterfall_model#Sashimi_model "Sashimi model at Wikipedia"
 [3]: http://en.wikipedia.org/wiki/Business-to-business "Business-to-Business at Wikipedia"
 [4]: http://en.wikipedia.org/wiki/Business-to-consumer "Business-to-Consumer at Wikipedia"
 [5]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-1 "Read the first article"
 [6]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-2 "Read the second article"
 [7]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-3 "Read the third article"
 [8]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-5 "Read the fifth article"