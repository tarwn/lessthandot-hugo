---
title: There Is Never Time For … (Part 3)
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2010-04-21T11:24:43+00:00
url: /index.php/itprofessionals/itservicemanagement/there-is-never-time-for-part-3/
views:
  - 4804
rp4wp_auto_linked:
  - 1
categories:
  - IT Processes
  - IT Service Management
  - Policy and Standards
tags:
  - process improvement
  - waste

---
Our ticket counts are down, the systems are failing less often, and we&#8217;re reporting regular corrective actions to the business instead of just patches. Fixing issues as they occur, even root cause correction, is a process of stabilization. I&#8217;m not sure about you, but I don&#8217;t like aiming for average.

_This is the third article in a three part series on major gaps that we never seem to have the time to address as well as ideas on how to overcome and initiate processes for closing or reducing those gaps. Each article is based on one high-level subject and provides ideas on how to gain individual and group level traction._

We will initially cover some key concepts in Process Improvement and Lean, then jump right into a PC Request process that is modeled on a real process I have worked with in the past&#8230;and in one pass we will cut the PC ordering process in half.

## Firefighting Is Not Our Job

Whether you&#8217;re patching surface issues or solving root cause problems, all you&#8217;re doing (in the paraphrased words of [Dr William E. Deming][1]) is catching the systems back up to either where they were or where they should have been. In the meantime, those services that support the company cost money while time and technology continues to improve and change the way competitors are doing their business.

On top of the inexorable march of technology there is a certain level of waste that accrues in every process. As time goes by small steps are added to processes that make sense at the time, but aren&#8217;t really necessary long term or could be minimized with the advent of other new policies, long practice, or new ideas. These wastes can consist of extra costs, time, movement, or even poor quality work and reducing the wastes can not only reduce costs but more importantly unlock more of our (and others) time. And just like improving one&#8217;s own skills and trying to standardize common operations, the benefit of starting process improvement is hard to quantify and requires time that we think we don&#8217;t have.

## Seven Wastes

The philosophy of Lean (or Lean Manufacturing) categorizes [seven types of waste][2]:

Waiting
:   Any time there are delays between the steps of a process we are creating waste. Delays between steps when no one is working on the task cause us to have to try and see farther into the future and provide people with longer quotes than they will be happy with. Plus it gives people more time to make minor changes or additions to their request, potentially requiring us to redo part of the work we had already completed.

Overproduction
:   Spending time and resources producing more of something than a company needs is a waste. That same time could have been spent doing absolutely nothing or, better, working on something that had direct value to the business. If we built or acquired something physical, then we also get a sneaky secondary waste because now we have to use up valuable space to store that item.

Unnecessary Motion
:   Excess walking, stretching, lifting, etc is not only a concern for safety reasons, but also a potential form of waste. As you walk back and forth to the same place 10-15 times a day consider how much time you could save if it was a shorter walk or no walk. Considering the focus many of us are required to bring to our tasks, consider how much smoother they would be if we weren&#8217;t constantly stepping out of our zone to go walk somewhere and back again.

Unnecessary Inventory
:   Excess inventory in the IT world is not just excess equipment, but can also mean excess tasks waiting on their next step. One example would be a backlog of new equipment that is waiting for OS and software installs before it can be usable, another would be a backlog of software tasks that are waiting on QA to validate them. There are costs involved in having unnecessary inventory of anything, physical equipment takes up valuable space and time to inventory and manage, tasks that are sitting around require more extensive tracking and management procedures and start getting in the way of other tasks that are coming in.

Over Processing
:   When the tool being used causes more work than should be necessary to complete a job, then we are in the realm of overprocessing. This waste can range from tools being poorly located in relation to one another, using more tool than is necessary for a job, and potentially overuse of the wrong tool simply to justify the expense in purchasing it. When we try to make a tool do a job it isn&#8217;t good at, we waste time and money with a risk of greater defect rates in our process or end unit.

Transporting
:   Excessive movement of a product or task is wasteful of time and risks additional wear, damage, or potential loss of the product or task. Even in development there is waste in transporting a task to QA and back multiple times and understanding why that task is going back and forth, which is why many Agile shops suggest collocation.

Defects
:   Making bad or incorrect products is a waste of the time and energy that went into making that product. Completing a task incorrectly or applying the wrong correction in response to a help request is also a waste of time and energy. Any time that we execute the wrong task or produce poorly, we are going to spend more time to determine it was the wrong thing, possibly why it was the wrong thing, and then how we can correct it or start again from the beginning. Doing this occasionally is waste, doing it regularly is an ongoing failure.

Seven wastes. How many of these looked familiar? Would you be surprised if I told you studies had shown that service businesses often ran at the 30-80% waste level? ([Lean Six Sigma For Service, Michael L. George][3]) What if your department was on the best side of that curve and could only manage to reduce waste by half, would a 15% improvement in cost or time be worth it? Reduced times from requests coming in to requests being fulfilled? What if your competitor did it?

## Process Improvement

There are a lot of process improvement tools out there, as well as a number of methodologies. Lean and Six Sigma, the two I am most familiar with, focus on waste reduction and process quality improvement respectively. Lean has a number of tools for identifying wastes in a process and evaluating them, while Six Sigma brings us tools for measuring the quality of a process and improvements to that quality. Being an expert at neither of these methods, I am instead going to outline some basic methods for evaluating a process and trying solutions.

### Evaluating a Process

When you are first starting out it will be easier to pick processes that you know are running slowly or that the business is commonly upset with. Later, once you have a bit more experience and a track record to show people, you should involve people that are directly interacting with IT processes and enlist them to help you find wastes in processes. Generally the people actually executing a process have a good idea when it isn&#8217;t working, however it is important to start evaluating without an end solution in mind. Many times when we can identify problem areas we think we already have a solution, only to find something an order of magnitude better during our improvement process.

Select a process that is commonly considered slow or costly to perform. Starting at the beginning of the process, for instance when the request is generated or when a system triggers a process, draw each step the task goes through on it&#8217;s way to completion and communications of that completion. At each step write down the average amount of time (you could also choose to write down the maximum, just be consistent). What you should end up with is something like this:

<div style="text-align: center; color: #666666; ">
  <img src="http://tiernok.com/LTDBlog/nevertime_workflow.png" alt="Sample Workflow Diagram" /><br /> Diagram #1: Partial sample workflow of PC request process
</div>

In our example workflow it takes almost 20 days from the first request to actually get hardware in so we can start installing software. At each step in the diagram we should be asking ourselves what value is provided by the step and why it takes as long as it does. For example, the most obvious items on the diagram are probably the time for filling out the form and generating the PO. Cutting those two in half would be a 13% improvement in the process time from the request to getting the equipment in. But lets look at a couple more, what value does it provide for the IT Manager to sign off on both the request and the Purchase Order Request? What value does it provide to quote each PC request individually? We now have 4 items to work on improving. 

### Improving a Process

So we have some targets to start improving on from our example above. The simplest tool for reducing the waste of a step in the process is to understand the purpose of the step. By understanding the purpose we can start paring away some of the waste that has attached itself to the process over the course of time. Each step should be evaluated against the goal of the process and determine what part (if any) actually provides some value towards that goal. 

_I like using the 5-Why process to dig into processes, it is simple, direct, and gets good results. A good article can be found [here][4]._

#### Example 1) The Request Form

The request form takes 2 days to fill out on average, partially because the end user has to ask a lot of questions about values on the form and partially because it&#8217;s so long it takes a day to get up the energy to get started. The value it provides is a clear checklist of everything the end user thinks should be on the final PC.
  
Normally people would look at the situation and ask if we could simplify the form, build a system, or better educate the end users. I would go one step further, can we get rid of the form? What if we created a standard configuration for each department and a standard list of applications for each management position? It still isn&#8217;t perfect, but would it be closer or farther then having the end user try to create the list for us for each individual PC?

#### Example 2) Generating the PO

The process of generating a PO currently takes 3 days. We identify the value of this process as having the funds to purchase the requested PC. Unfortunately we have over-simplified the process of generating a PO by making it only one step in our diagram and I am sure the person responsible for generating them would have a few words to say about that. Instead of trying to guess, walk down and have a conversation with them. If possible map out the process they are going through, from receiving a request to completion of the PO. If you can get them on board and reduce the process, then you will be making a company wide improvement and I expect to see &#8220;Cross-Functional Process Improvement reduced processing of Purchase Orders by 50% company-wide&#8221; on your year end list of achievements. For the mean time, lets put this one to the side, as we won&#8217;t be able to fix it until management sees some good savings from process improvement and we&#8217;ve built up a little credit.

#### Example 2b) Generating the PO

Wait, what? Again?
  
While we could help the PO process improve, our goal today is to try and improve our example process, so lets look again at what value we are achieving. Earlier we identified the value as being able to purchase the PC. Can we standardize a little? How much of our PC purchases fall into a pattern? We&#8217;ve undoubtedly budgeted for a certain degree of computer equipment throughout the year based on expected labor growth or turnover, would finance allow us to create blanket POs to cover this expense? If we could standardize these PC purchases and use blanket POs, then we have all the value with no cost to this process. A blanket order for new hires and a second for PC replenishment would could cut out 4 Days, approximately 20% of the current process.

#### Example 3) IT Manager Sign-off

Assuming we hadn&#8217;t simplified the Purchase Order process yet, we had two different instances for the IT Manager to sign-off and in both cases it is taking him/her a day to do so. Before we jump into color-coding labels or special desk trays, lets take a step back and look at why they are signing twice. Is the first signature necessary? What value is it providing? If it is simply an acknowledgment of the request, do they need to do it or can they delegate that to the person processing the request? Potentially 1 day removed (5%). Did we remove the need for the per-order PO? Then that 1 day is definably removed.
  
And that&#8217;s one less item to bug your manager about in your weekly meeting. Less stressed managers are happy managers, happy managers are much better than the alternative.

#### Example 4) Quoting the PC 

What is the value of quoting the PC? Finding out how much it will cost. Going back to our second example, is there any way we could create some standards? How many PCs models do you need? Most vendors are willing to help you build a standard set of PCs with consistent quotes. Does each individual member of the company need their own special setup, or would a few standard models suffice? If the value of this step is to determine the cost and we can create some standard model configurations, then we just removed the custom quote step and reduced delivery time by another 2 days (or 10%). 

### Results

In our example above we saw the potential for reducing the first half of our new equipment process by almost 50%. These types of numbers are just as achievable in the real world as they are in the example, the difference is going to be that the real world will have more strange extra steps involved and it isn&#8217;t always possible to completely replace or remove a step. Focus on adding value to the final deliverable and try replacing complex systems with simple ones. Sometimes all it takes to cut a couple days off a process is to have a labeled tray on someones desk or a clear plastic pouch to put documents in on their front door.
  
As we make improvements we need to communicate them to the wider company. By continuing to pare away at the wastes inherent in your processes, your area (and the company) is going to be running more smoothly and more effectively. 

The next step is to grow the process, to be an agent of change in your business. There are others that are or will be interested in continuous improvement, but as you start to widen the scope you need to get buy-in from managers and executives. Having real-world examples of the savings the company has already achieved is going to be a strong argument for taking time away from day-to-day tasks and applying it towards a cultural shift that is an investment in [an unknown ROI][5].

## Why Improve&#8230;

While it may take more research and thought to identify wastes and start improving your environment, while it may seem easier to simply stand still and keep throwing more technology at problems as they appear, remember that there are companies adopting practices for improvement as part of Agile, Lean, and other cultural shifts. At the worst you will be increasing your effectiveness, decreasing costs, and improving your understanding of the business, at the best you could be starting a company-wide culture shift that requires managers to shift towards leadership and servant-leader roles, requires greater levels of respect for non-managers from the business, and enables businesses to provide high quality results at competitive prices, even when those competitors are offshore at much lower wages.

And the last benefit? Most of us are in our jobs because we like identifying problems and finding solutions for them. Process Improvement is just one more playground that we have available to us, with new and interesting problems that generally benefit from the simplest, most elegant solutions you can come up with. Sure, you&#8217;re going to have to get up and talk to people. But they will be looking for you at some point anyway, so we might as well save some money, sharpen our skills, and head out to meet them first.

Miss an article in the series?
  
There Is Never Time For &#8230; [Part 1: Training and Development][6]
  
There Is Never Time For &#8230; [Part 2: Standard Processes][7]
  
There Is Never Time For &#8230; _Part 3: Process Improvement_

 [1]: http://en.wikipedia.org/wiki/W._Edwards_Deming "More on Dr Deming at Wikipedia"
 [2]: http://en.wikipedia.org/wiki/Lean_manufacturing#Types_of_wastes "Seven Wastes at Wikipedia"
 [3]: http://www.amazon.com/Lean-Six-Sigma-Service-Transactions/dp/0071418210 "View book at Amazon"
 [4]: http://lssacademy.com/2010/01/10/introducing-the-5-why-“so-what”-test/ "View the article at LSS Academy"
 [5]: http://dailykaizen.org/archives/792 "How do you calculate ROI? You don't!"
 [6]: /index.php/ITProfessionals/ITProcesses/there-is-never-time-for-part-1 "Read the first article in the series"
 [7]: /index.php/ITProfessionals/ITProcesses/there-is-never-time-for-part-2 "Read the second article in the series"