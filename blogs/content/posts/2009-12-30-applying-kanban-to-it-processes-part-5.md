---
title: Applying Kanban to IT Processes (Part 5)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2009-12-30T12:58:17+00:00
ID: 654
url: /index.php/itprofessionals/itservicemanagement/applying-kanban-to-it-processes-part-5/
views:
  - 15667
rp4wp_auto_linked:
  - 1
categories:
  - IT Processes
  - IT Service Management
  - Project Management
tags:
  - kanban
  - process improvement

---
_This is the fifth and final article in a multi-article set that describes the basics of Kanban and explores applying Kanban to IT Processes. [Part one][1] provides a basic overview of Kanban and how it is used in manufacturing. The remaining parts explore sample scenarios to help generate ideas for your own environment._

The first four articles discuss the basics of Kanban and follow it's application in three fictional environments. In each of these stories the implementation is successful and there are positive improvements in a fairly short period of time. This final article briefly discusses some common challenges and the concept of continuous improvement. Unlike previous articles in the series, this one does not have illustrations, so I apologize in advance to those of our members that prefer a higher picture-to-text ratio in their reading material (\*cough\* George).

## Implementing Kanban

The fictional stories in previous articles all followed a common theme, successful improvement of the process with few or no challenges. However for those of us working in the real world, we are all too aware that no plan or process can be launched without challenges. While addressing all of the potential challenges in each reader's environment may be well beyond the scope of both my skill and this article, there are two common challenges to Kanban that most of us will face in IT environments.

### Changing Habits

In addition to the challenges of defining our processes and attempting to standardize tasks that are often the exceptions to standards, following a Kanban process will also force us to challenge some ingrained habits. The idea of limiting the amount of Work in Process (WIP) in a step may sound good in an article, but actually submitting to limits is challenging the first few times . Many management styles have a tendency to focus on how much time is spent working or how many tasks an individual is completing and, with these styles, stopping work in a single step is akin to putting your feet up on your desk and taking a nap.
  
Kanban forces us to face these measurement systems and correct some of the flaws they drive into our processes. Rather than measure how hard an individual is working in a single step, we measure how well we deliver through the entire process. Rather than measure how many hours an individual is working, we can measure how many hours of work we have removed from the process. Where the old measurement system is asking why we aren't piling up more work in between development and QA, Kanban is forcing us to question how we can improve the throughput or decrease the rework rate.
  
Ingrained habits are difficult to change. Instead of challenging these beliefs directly, start by modeling the process and tasks that are flowing through them. There are separate challenges in this modeling process, but once the visualization is in place and you start getting others involved in refining that visualization and tracking tasks through it, many of these challenges will go away. The ability to see the tasks flow through the process fulfills a need that many people have, providing visibility and insight into the process that previously was ruled by estimation and occasional guesswork.

### “IT is not Manufacturing”

Another common challenge to Kanban is the fact that, having originated in Manufacturing environments, it is not suitable for an IT environment. This is a widely stated argument against Kanban (and Lean principles in general) and it has been soundly disproved in a wide number of industries. The idea that practices or knowledge from one industry cannot be used to further our understanding in others seems a bit egotistical, especially when coupled with arguments that any one area is too specialized to learn anything from others. If this were truly the case then the medical advances we gain from the space program would never have occurred, and neither would hundreds of other concepts and ideas that have crossed industry boundaries to breed new ideas or products in other, dissimilar industries.
  
Occasionally the argument is couched in less direct terms, citing the difference between manufacturing plants producing discrete, repetitive parts and the constantly changing tasks in an IT environment. Given a few minutes with your favorite search engine, you will find examples in a number of industries, including software development, where Kanban has not only been implemented successfully, but has led the way for continuous improvement and other practices with roots in Manufacturing. While that may not be the most philosophical answer to the challenge, I don't see where out time is well spent formulating a logical proof for something so easily observed.

Applying Kanban is not rocket science, but it does require thought and it does require time. Luckily there are whole communities of people out there who are willing to answer questions, refer experts, and suggest good books. 

## Why Kanban

The previous articles showed Kanban working in fictional environments, but did not spend much time explaining how or why it was working the way it did. The articles do not share details on the methods the teams use to isolate targets for improvement or how those improvements are determined. Making changes in real processes can be both as easy as the stories, and quite a bit more challenging. What we get from Kanban is a tool that help locate trouble spots or areas to focus our efforts for greatest effect.
  
As we implement Kanban, we begin to complete tasks in a consistent manner. This consistency improves our execution speed because we can make assumptions about the tasks we receive; they meet a certain level of conformity and have a certain common set of requirements and characteristics. This reduces the number of times we have to return to earlier steps, reroute tasks through new steps, or invent processes on the fly. The act of creating the Kanban board helps drive us to find or build that consistency, and further reduces some of the overhead of determining where tasks are to be routed or their current statuses. The board provides a high-level view of cards or groups of cards moving through the process, bringing to light bottlenecks and eddies in the process flow that are not visible from inside the process. Root cause analysis of the bottlenecks shows us areas where a step improvement will directly correlate to a process wide improvement.

### Continuous Improvement

The term 'Continuous Improvement' shows up in several of the articles and the concept is present in all of them. The principle of continuous improvement is to make small, incremental improvements to a process that, over a long term, build towards great savings and quality improvements. Individual improvements are made by finding and analyzing a waste in the process (such as wasted material, movement, safety hazards) or many wastes in a single step of the process (such as the defect testing in the software development article). By determining the root cause of the waste or wastes, and making small process improvements to reduce or remove that waste, targets or methods for improvement become available. By slowly paring away at the wastes in our processes, we improve the quality, cost, and timing characteristics of our process. 

Kanban helps Continuous Improvement efforts by providing a consistent flow and visualization of our process. The visualization and standardization of the process cause exceptions and bottlenecks to become more visible, giving us focal points for our improvement efforts. Once improvements have been made, the Kanban board can then show us how those changes affect the greater process and if the improvements have improved the overall process, uncovered new issues, or failed to solve the root problem.

### Quality and Cost

While Kanban provides us with the ability to locate and improve process issues that reduce our delivery time, it does not necessarily provide tools for reducing cost or improving quality. While we saw quality and cost improved in the example articles, this was often as a byproduct of improving a cycle time bottleneck. Kanban does provide us some guidelines that can be re-used, however. As with cycle time, if we want to improve quality or costs we need to use our process and track where our costs and quality problems are realized and focus on these areas.

#### Quality Improvement Example

For example, if we want to concentrate on quality we can create standards defining the level of quality in our end product and begin testing against them. As we test, we can track the cards (or lots) that fail the quality testing and return for rework (article 3) or are scrapped (thrown out) and later replaced by additional work. Using the frequency of the problems and the cost used to produce the task before each quality problem was found, we can create a fairly simple cost distribution to determine our greatest opportunities for improvement. In some cases we can design quality issues out of the process, in others we can drive the testing earlier in our process to reduce the amount we are investing before problems are found.

#### Quality/Time/Cost – All Please

In IT, we often quote the cost/quality/time triangle to people who expect all three with no additional work or investment. Using the example above, we could easily replace quality with cost and build a simple method for finding the greatest opportunities for cost improvements. Because wastes often effect more than one of these legs of the triangle, we could choose to improve our cycle times, quality, or costs and generally improve the others as a byproduct.
  
It is not uncommon in the manufacturing world to decrease quality problems, timing, and cost far enough to improve capacity 25-50% without hiring new additional employees or purchasing new equipment. In IT we often count on technology to drive the improvements, but technology is something that any of our competitors can buy and availability is dictated by the market, not our needs. To get ahead of our deadlines and competitors, to get our vacations back, or to get annual raises that are above the annual inflation rate, we need to go beyond what the vendors are offering and create a higher quality of service and business engagement. Reducing costs, improving quality, and reducing delivery time all affect the bottom line, improve the effectiveness of the rest of the business, and look good on the end of year review. And in the event that a company is less interested in effectiveness and more interested in the number of hours on the clock, cumulative improvements also make excellent bullet items on resumes.

## More Opportunity

There are many other opportunities for Kanban than the ones outlined in the other articles, and many other articles providing information and background on Kanban. The three examples in prior articles were chosen to display a wide variety of problem spaces:

  * A support environment is handling an unknown number of tasks on demand, each consisting of a completely different set of tasks and work
  * A PC deployment process that is similar to manufacturing but requires two people to run multiple steps with a process that is only used every year or two
  * A software development process that defines their own tasks as subsets of greater features or modules

It is hoped that these three examples are close enough to other tasks in our respective departments that we can see ideas on how to apply Kanban to our own areas and tasks. Other ideas include:

  * A report designer can define a common process of designing, developing, revising, and deploying reports and start tracking down inefficiencies that bog down the process.
  * A database administrator can start looking at their transaction queue mechanisms and create a Kanban-like visualization of the process to help clear the bottlenecks by focusing improvements where they would count the most.
  * A Systems Administrator can design a system review and upgrade schedule for each of their functional areas and start looping the entire list of areas through that process, each time reducing the work or time involved in evaluating new technology options or implementing patches and maintenance.

This series is not going to be the last time I talk about Kanban, but I will also discuss a number of other practices, philosophies, or technologies. If there are further questions, topics of interest, or even if you want to argue about the efficacy of Kanban, feel free to post in the <a href=""http://forum.ltd.local" title="To the Forums!">Forums</a>. 

### Thanks

I would like to thank everyone that has helped me review and edit these articles. Over the course of their publication I have gone from a week ahead to writing a few days prior to publication and the help of several members has been invaluable:

  * Tim (Riverguy) and David (Thirster), who caught some critical errors in the first couple articles
  * Erik (Emtucifer), who provided a very thorough review of the first article
  * Fionnuala (Remou) who has provided reviews for later articles, and saved me from editing one article to death
  * Ted (onpnt) for reading (or pretending to read) each article and providing frequent positive feedback

If you have missed prior articles in the series you can find them here:
  
Applying Kanban … Part 1: [Kanban Overview][2]
  
Applying Kanban … Part 2: [Kanban applied to Tech Support][3]
  
Applying Kanban … Part 3: [Kanban applied to PC Deployment][4]
  
Applying Kanban … Part 4: [Kanban applied to a Development Group][5]
  
_Applying Kanban … Part 5: Challenges, Additional Concepts, and Wrapup_

 [1]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-1
 [2]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-1 "Read the first article"
 [3]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-2 "Read the second article"
 [4]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-3 "Read the third article"
 [5]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-4 "Read te fourth article"