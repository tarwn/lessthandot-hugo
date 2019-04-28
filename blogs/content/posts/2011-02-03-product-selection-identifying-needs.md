---
title: Product Selection, Identifying Needs
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2011-02-03T09:40:00+00:00
ID: 1017
excerpt: |
  Product selection is something we will all run into in our careers, regardless of where we fall in the technical spectrum. Whether we are helping select a tool for end users, new hardware for our servers, or a framework to use in our process, product selection can have far reaching affects and the potential to consume a great deal of time and effort.
  
  The following post is the first of four outlining a four step selection process that attempts to create structure without becoming too heavy to use. There are meetings and details, but only in support of a few key methods (chosen due to their success in my own anecdotal experiences).
url: /index.php/itprofessionals/projectmanagement/product-selection-identifying-needs/
views:
  - 8641
rp4wp_auto_linked:
  - 1
categories:
  - IT Processes
  - Project Management
tags:
  - business analysis
  - process
  - product selection

---
<div style="background-color: #eeeeee; padding: 1em; margin: 1.5em .5em 0em 0em; border: 1px solid #CCCCCC; float: left">
  <h4>
    Product Selection:
  </h4>
  
  <ul style="margin-left: 1em; list-style-type: none; ">
    <li style="color: #666666; font-style: italic; font-weight: bold;">
      1: Identifying Needs
    </li>
    <li>
      2: <a href="/index.php/itprofessionals/projectmanagement/product-selection-requirements-and-scoring" title="Read the 2nd entry">Requirements and Scoring</a>
    </li>
    <li>
      3: <a href="/index.php/itprofessionals/projectmanagement/product-selection-evaluation" title="Read the third entry">Evaluation</a>
    </li>
    <li>
      4: <a href="/index.php/itprofessionals/projectmanagement/product-selection-reviewing-the-process" title="Read the 4th entry">Review</a>
    </li>
  </ul>
</div>

Product selection is something we will all run into in our careers, regardless of where we fall in the technical spectrum. Whether we are helping select a tool for end users, new hardware for our servers, or a framework to use in our process, product selection can have far reaching affects and the potential to consume a great deal of time and effort.

The following post is the first of four outlining a four step selection process that attempts to create structure without becoming too heavy to use. There are meetings and details, but only in support of a few key methods (chosen due to their success in my own anecdotal experiences). Like any process, this comes with no guarantees. Try the whole thing, takes pieces and incorporate them into your own process or throw the whole thing out and keep your own process. The important thing is to stop shooting from the hip so we can cut out some meetings, reduce surprises, improve integration, and ultimately select a product that does what we need it to do without an emotional and political bloodbath.

## Step 0: Why Do I Need a Process?

<div style="float: right; margin: .5em; padding: .45em; border: 1px solid #dddddd; color: #666666; font-size: 80%; text-align: center;">
  <img src="http://www.tiernok.com/LTDBlog/ProductSelection/AorB.jpg" alt="Option A or Option B?" />
</div>

It often starts with “I need an IDE” or “I need ABC, Inc's IDE”. On the one hand, when we pre-select a product we then measure all other potential solutions against that product instead of against our needs. On the other, a vague category fails to provide anything but the most general criterion. Despite their differences, they tent to both lead to a very similar outcome: highly opinionated, emotional selection processes where groups of people end up rallying behind the product they like the best. These methods are extremely variable and, beside creating a lot of dissension and wasted time, often leave us with a product that is missing a number of critical features or capabilities and requires far too long to integrate into our environment. This method may seem faster, but it has a lot of additional costs and can actually take far longer.

## Step 1: Identification

Whether we have a specific product or type of product in mind, the first thing we should is take a step back and determine what need we are expected to fill. Identifying the needs provides us the most important criteria during product selection and provides a tool to separate the requirements from the nice-to-haves.

### Identify the Need

Rarely are we asked to look at a product simply because someone wants one more product on the shelf, or needs to waste some money from the budget. Before we start looking at products we need to determine what the root need is for that product selection. Needs should be expressed as barriers that prevent our company from doing it's job at a higher level of effectiveness, ongoing problems that are degrading performance or creating additional work, new standards, changes in legal restrictions, etc. 

By using [Root cause analysis][1] methods we can dig into the reasons behind the project and determine then actual barriers or needs the business is trying to address. These are often not very clear to the person who initiated the product request, but determining the root need will take us a long way towards succeeding in filling that need.

### “The CEO Said So”

<div style="float: right; margin: .5em; padding: .45em; border: 1px solid #dddddd; color: #666666; font-size: 80%; text-align: center;">
  <img src="http://www.tiernok.com/LTDBlog/ProductSelection/dashboard.jpg" alt="Dreaming of a dashboard" style="max-width: 200px" /><br /> Are they really asking for<br />more software?
</div>

Often we will run into reasons such as “because the CEO said so” or “because we budgeted for it” as needs. We can these items, but we don't want to let the position or invocation of a budget keep us from digging into the root cause. Often when a CEO says “we need a new dashboarding system” what he is actually doing is communicating a need such as “I don't feel that I have visibility of X and in my past company product Y gave me that visibility”. While he thinks he is asking us to buy a specific product, what he is actually doing is asking us to to solve his visibility problem using the solution that satisfied him the last time he had this problem. If we determine which capabilities or features he most greatly misses, we can then determine what those features provided at the CEO's last job and likely what they feel they are missing now. 

Note that this is the same reasoning that our new IT manager uses when he joins the company and starts buying all of the software he used at his last one. He is uneasy or has a need and knows what products made him feel comfortable or worked in a previous environment, so those are what he purchases. Unfortunately, this isn't his last position and environments can vary drastically under the surface. Like the CEO, we want to dig into what need or uneasiness they are trying to resolve and use the root needs as criteria or as leads to follow-up with other business owners.

### “We Always Use Vendor X”

Not uncommon, many companies like to use a single vendor or a selection from a known set of vendors. The advantages include the presence of an existing relationship, potential for extra savings, and simpler support plans (the same number and people we already call).

All of which is wasted if what they are selling is different from what we think we are buying.

This is a difficult situation to work with, because often people will see having a selection process to be a waste of effort. However we can play off the fact that there isn't a clearly defined need and even if we are purchasing from that vendor, we need to make sure we buy the correct package, support options, and integration services. And in order to do that we need to evaluate our own needs and see what other, similar companies offer to make sure we are getting all of the options we need. And now we're back on track, defining a need instead of impulse buying.

### (And Other Tricky Situations...)

These are not the only tricky situations, but the key is to use these situations to the opposite effect that people are expecting. If the product selection is a foregone conclusion, then we use the process to select options inside that pool and engage that person or persons in the selection process. If we are seen as protecting their interests and needs (which we are, by the way, just in a broader fashion then they are expecting), then we can spend less time arguing and more time helping them get what they really need.

### Next Steps

Once we have identified the root needs that need to be resolved we can start to bring together the people that need to be involved. We have a guiding direction, now we need to build a collection of selection criteria, determine which among them are requirements, and determine the relative merits of the remaining nice-to-haves. The goal of our next step will be to produce a scorecard we can apply to all of our prospective products in an objective manner.

#### Product Selection:

<ul style="margin-left: 1em; list-style-type: none; ">
  <li style="color: #666666; font-style: italic; font-weight: bold;">
    1: Identifying Needs
  </li>
  <li>
    2: <a href="/index.php/ITProfessionals/ITProcesses/product-selection-requirements-and-scoring" title="Read the 2nd entry">Requirements and Scoring</a>
  </li>
  <li>
    3: <a href="/index.php/ITProfessionals/ITProcesses/product-selection-evaluation" title="Read the third entry">Evaluation</a>
  </li>
  <li>
    4: <a href="/index.php/ITProfessionals/ITProcesses/product-selection-reviewing-the-process" title="Read the 4th entry">Review</a>
  </li>
</ul>

 [1]: http://en.wikipedia.org/wiki/Root_cause_analysis "Root Cause Analysis on Wikipedia"