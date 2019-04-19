---
title: Applying Kanban to IT Processes (Part 3)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2009-12-15T15:14:00+00:00
ID: 650
url: /index.php/itprofessionals/itservicemanagement/applying-kanban-to-it-processes-part-3/
views:
  - 21602
rp4wp_auto_linked:
  - 1
categories:
  - IT Processes
  - IT Service Management
  - Project Management
tags:
  - kanban
  - measurement
  - process improvement

---
_This is the third article in a multi-article set that describes the basics of Kanban and explores applying Kanban to IT Processes. [Part one][1] provides a basic overview of Kanban and how it is used in manufacturing. The remaining parts explore sample scenarios to help generate ideas for your own environment._

In this, part 3 of the “Applying Kanban to IT Processes” series, we are exploring the use of Kanban to manage a short-term functional project. This example focuses on using Kanban to create a transparent process to track the flow of equipment through a number of complex steps, without incurring additional costs for tracking software, complex processes and training, or duplication of effort. Improved uniformity or quality of the deployment process will also help improve efficiencies in troubleshooting and repair times as well as ensure a document-ably high level of conformance to software and licensing standards.

## Welcome to DEF's Annual PC Deployment

DEF Inc is a small to medium company with roughly 1000 PCs and laptops actively deployed. Their IT group works like most groups this size, with a high level of individual management, strong team ties, and a relatively small number of IT personnel covering a wide variety of roles. Of the 15 active IT people, only 2 will be working on the PC deployment, and they have been given a high level of latitude in creating a process to get the work done. Clear, simple reporting of progress and successes is determined to be a requirement by the team, to prevent their hard work from being an [invisible project][2]. Finally, there is limited space for undeployed systems, so the team will need to carefully manage the number of undeployed systems and purchase orders for new systems.

### The Equipment and Tasks

The annual PC deployment is expected to account for the following equipment:

  * 160 new PCs to be deployed
  * 40 new laptops to be deployed
  * 120 PCs and 10 laptops to be refreshed and redeployed

Software imaging is available to install a base operating system and set of drivers on each PC, reducing the variability in initial install process. Tasks like domain registration, utility software, office software, and IT software installations will be standard for every system, but there will also be some customized software installation based on the final user or area the equipment will be assigned to. Refreshing an existing PC requires the same steps with the addition of an extra task in the audit system to link the old and new use of the system. Based on stories from prior years it is clear that one of the most disruptive issues was when a system required *rework due to a missing software installation or configuration, so the team decides to add some verifications to the process to limit opportunities to miss software installs or configurations.

<div style="background-color: #eeeeee; padding: 1em; font-style: italic">
  Rework is a name for the process of taking a completed part and sending it back through a portion of the process to correct an incomplete or missed production step. In the case of deploying a PC this could be a missing piece of software during the setup process, improper permissions, or any of a dozen other items that would require a technician to return to a previously 'completed' PC in order to finish working on it. Costs from rework include the cost of executing a step a second time, executing later steps a second time, changeover between the current work and the item to be reworked, and delays in completing the current work against the original estimate or deadline.
</div>

### First Visual Board

After discussing the requirements and steps in the process, the technicians have created a common process for all equipment:

  * Receiving – New equipment and equipment for re-deployment that has not yet been 'tagged'
  * Inventory – New equipment and equipment for re-deployment that has a 'tag' applied
  * Imaging – Applying basic OS, driver, and software images to the systems
  * Customization Analysis – Building a list of customer software and/or settings for the intended end-user
  * Office Software – Installation and initial configuration of office software and company templates</li? 
      * IT Software and Utilities – Installation of a standard set of utilities and IT software for troubleshooting and auditing
      * Custom Installation – Installing and applying custom configurations
      * Audit – Running the first Audit of a PC and linking assignments for re-deployed PCs
      * Deployment – deploying the hardware to the end user

To manage PCs through this process, the team has invented a system of 'tagging' the equipment. The technicians have designed a combination check-list and worksheet that is copied and filled out for each PC. The top corner of the document becomes the Kanban card, while the rest serves as a checklist that will be used to verify the previous step of each deployment stage as the equipment progresses through the process. In prior years the team determined there was an average of 5% rework required to correct missing configurations or software.

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <img src="http://www.tiernok.com/LTDBLog/Pt_3_Tag.png" alt="example worksheet" title="Example Worksheet" /><br /> Example worksheet with cut-out for Kanban card
</div>

Rather than putting a QA step at the end of their deployment process, they have chosen to start each step by verifying the task from the previous step. By verifying sooner they hope to prevent the need for a long QA process, reduce the amount of rework, and catch and correct issues earlier to prevent repetition of later tasks (such as running the audit software). Check boxes for 'execute' and 'verify' are available for each line on the tag.

Now that the tasks have been defined, the first visual board is put together using tape and labels on a corkboard. Thumbtacks will be used to attach the Kanban cards for each piece of equipment to the appropriate step on the board. 

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <img src="http://www.tiernok.com/LTDBLog/KanbanPt3_VisBoard_1.png" alt="Kanban board" title="Kanban Board" /><br /> Kanban board with some example cards on it
</div>

The Kanban limits at each stage are based on the available workspace and the desire to keep equipment flowing regularly through the processes. The team has decided that to balance between consistent flow (small limits) and the additional effectiveness of running installers in parallel (larger limits), they will define limits based on the length of the software installs in order to ensure the longest steps also have the highest throughput and don't become delays for later steps. Imaging, office software, and auditing require little human involvement but large amounts of time, while Customization Analysis, Deployment, and the remaining two software installation steps require higher levels of interaction and attention from the installer. 

### Creating a Report

There are a number of requirements and factors available to report on with this process. Based on the requirements and expressed wishes of the IT Manager, the team produces the following list of items to report on:

  * Progress – based on the concept of a [burndown][3] chart, the Progress chart displays actual progress and a projected completion date
  * Inventory to Deployment – a simple graph to show the actual deployment times against the averages and range from prior years
  * User Satisfaction – A pair of horizontal bars that display successful delivery to promise date and number of incomplete deployments (rework)

<div style="text-align: center; font-size: .8em; margin-bottom: 1em;">
  <img src="http://www.tiernok.com/LTDBLog/KanbanPt3_Spreadsheet.png" alt="Charts for Report" title="Charts for Report" /><br /> Spreadsheet and Charts for Weekly Report
</div>

Generating the report will require updating the spreadsheet at the end of each week with the number of actual PCs completed, incrementing the total amount in a second cell (if any occurred), and indicating the number of PCs that did not meet their target delivery date in a third cell. The projections automatically update based on an equation in the spreadsheet and the information is reflected in the charts. A few simple copy and paste operations places the graphs in last weeks document and the new weekly report is done.

## Final Analysis

During the execution of the deployment process the team tweaked the Kanban limits a little and made minor changes to the reporting mechanism, but did not have any large changes to make to their process. Given the transparency of the work, they were able to stay on track and deploy equipment at a regular pace. As they adjusted to the rhythm of the process they were able to start timing new PC orders to coincide better with inventory depletion, allowing them to increase their order size and take advantage of improved quotes on the equipment without running out of work in between orders. A final report was created with the final charts from the weekly reports and additional information on spending and labor reductions.

### Process Flow

New and re-purposed equipment was assigned a user and tagged prior to any work taking place. This prevented issues that had occurred in prior years, such as mis-assignments, double assignments, and re-assignments with the wrong software. The process also provided an accurate inventory of available equipment at all times, so that questions could be answered immediately rather than requiring someone to go to the work area and perform an inventory. Early communications with the end user on when the equipment would be available reduced the difficulty of deploying the PCs when they were completed, as end users had earlier warning and could help better define their availability, with the secondary result of fewer rescheduled installations compared to prior years. Immediate verification of prior steps and Kanban limits ensured that equipment flowed smoothly through the process, providing the bases for more consistent and repeatable cycle times. Better estimation of the deployment process time (inventory space availability) allowed for direct budget savings by allowing the team to increase the size of their orders and take advantage of better savings for the same amount of equipment. And finally, the reporting mechanism the team created was simple enough that the IT manager was able to use it in departmental meetings as part of the overall IT status report, without staying any later the night before the meeting.

### Achievements

Here are some of the achievements:

  * 100% Visibility – Visibility into the process and inventory at all times with no additional effort spent on tracking down technicians or counting hardware on the fly
  * Improved Morale – Reduced confusion and negativity from the end-users around the assignment process based on clearer communications and assignment process
  * Time Savings – no rework meant time savings in the deployment process as well as removing the interruptions that rework caused
  * Financial Savings – better inventory estimation and management allowed for larger order sizes and better savings without impacting completion times
  * Good Marketing, Cheap – the simple process of creating reports not only saved time but also produced simple enough metrics that they could be re-used at management meetings for additional positive press without requiring additional time and effort from the IT manager
  * Higher Productivity – Based on process consistency, estimation improvements, and reduction of the interruption level of prior years, the overall process took fewer labor hours to complete
  * Measurable Improvement – Due to careful tracking, the same statistics tracked during the process execution can be reported at the end of the year as department and personal improvements

The achievements listed here range from hard (financial) to soft (morale) and likely do not cover all of the achievements that would have occurred in a non-fictional version of this story. In a nutshell, the process was able to run more smoothly, more effectively, and more predictably, and that gave the team a number of measurable improvements over prior years. This same process will be re-usable in future years, and can be used as a foundation for further improvement or allow the improvement efforts to focus on other processes while this one simply runs smoothly forward. While using a Kanban system for a temporary process is not a common application, it shows another angle on how it can be used to tame complex work processes and help drive overall improvement of the process.

## Next Week

In the next article we will explore the usage of Kanban by a software development team as they seek to deliver and maintain a set of products. While there are already many non-fictional accounts of people applying Kanban to the software development process, development is a notable piece of the IT Department and I would be remiss if I did not provide my own spin on how Kanban can be utilized by a development team.

If you have missed prior articles in the series you can find them here:
  
Applying Kanban ... Part 1: [Kanban Overview][4]
  
Applying Kanban ... Part 2: [Kanban applied to Tech Support][5]
  
_Applying Kanban ... Part 3: Kanban applied to PC Deployment_
  
Applying Kanban ... Part 4: [Kanban applied to a Development Group][6]
  
Applying Kanban ... Part 5: [Challenges, Additional Concepts, and Wrapup][7]

 [1]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-1
 [2]: /index.php/ITProfessionals/ProjectManagement/an-invisible-project-is-a-failed-project
 [3]: http://www.controlchaos.com/about/burndown.php
 [4]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-1 "Read the first article"
 [5]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-2 "Read the second article"
 [6]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-4 "Read te fourth article"
 [7]: /index.php/ITProfessionals/ITProcesses/applying-kanban-to-it-processes-part-5 "Read the fifth article"